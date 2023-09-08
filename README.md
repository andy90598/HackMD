# AVM QT 筆記

## 09/06後的 AutoSys更新方式有變動
* 只需要把AutoSys給的那一包avm_usb放進 rootfs/rootfs_mtd/ 即可，不需要其他操作
* 另外一包 code資料夾 裡面那四個資料夾外加一個MakeFile檔案放進去 application/customer_app資料夾裡面
* 若是需要動到他們的code 就在customer_app/ 底下直接make clean;make 就好
* make 完之後東西在out資料夾 把 out 資料夾所有的檔案複製到 avm_usb並覆蓋就好了 

## 記憶體洩漏部分 (重要)
* Segmentation Fault 記憶體洩露就會當機
* 看log 會顯示在哪裡錯誤
* 導致錯誤的原因是因為沒有刪除乾淨
* 只要new出物件 就一定要delete該物件並且要把他=null
* 可以用QT的智慧指標就不用額外做delete跟 =null的動作
* 好用的參考文章 智慧指標 https://www.gushiciku.cn/pl/pt77/zh-tw
* 好用的參考文章 記憶體洩露原因 https://vocus.cc/article/6362798ffd897800019365f4

## git 部分 
* 在自己的分支開發完後要轉到115分之用cherry-pick
* 如果出現衝突 git status查看衝突
* 如果確定要完全保留自己或是他人的 git checkout --theirs/ours 去決定要保留原先的還是cherry-pick過來的檔案
* checkout完之後記得git add 檔案
* 然後git cherry-pick --continue
* 否則要手動解決衝突

## makeFile部分
* 0906給的那版code資料夾部分要放在application/customer_app底下
* 如果make 不過 要改一下 avm_ui_final_lib資料夾底下的 MakeFile
* y94
* QMAKE = 改成自己qmake 的路徑
* 再來就是在終端機底下輸入

```bash=
在 application\customer_app\avm_ui_final_lib底下輸入

# /SSD_4/andychou_ssd4/el-886-mgd1/application/tools/qt/host/bin/qmake 
# 這段要改成自己的路徑

/SSD_4/andychou_ssd4/el-886-mgd1/application/tools/qt/host/bin/qmake QMAKE_CXXFLAGS+=-Wno-reorder QMAKE_CXXFLAGS+=-Wno-unused-parameter QMAKE_CXXFLAGS+=-Wno-unused-variable QMAKE_CXXFLAGS+=-Wno-unused-but-set-variable QMAKE_CXXFLAGS+=-Wno-expansion-to-defined QMAKE_CXXFLAGS+=-Wno-deprecated-copy CONFIG+=ENABLE_APP_DEBUG CONFIG+=ENABLE_VIDEO_IN CONFIG+=ENABLE_ANDROIDAUTO CONFIG+=ENABLE_ANDROIDAUTO_3_0 CONFIG+=ENABLE_ANDROID_AUTO_VERSION_V3_0 CONFIG+=ENABLE_ADJUST_ALTER CONFIG+=AA_WIRELESS CONFIG+=ENABLE_CARPLAY CONFIG+=ENABLE_DUAL_SCREEN CONFIG+=ENABLE_1920_720_UI CONFIG+=ENABLE_IPOD CONFIG+=ENABLE_AUTOPLAY CONFIG+=CONFIG_GLB_GMNCFG_ENABLE_DEMOMCU CONFIG+=AD_INIT_ENABLE CONFIG+=ENABLE_WIRELESS_CP CONFIG+=ENABLE_WIRELESS CONFIG+=BT_RG440 CONFIG+=BT_PHONE_AUDIO_BY_PCMINTERFACE CONFIG+=WIFI_RTL8821_CS CONFIG+=APPLE_CARPLAY_ENHANCED_SIRI CONFIG+=APPLE_CARPLAY_ENHANCED_SIRI_INSTANT_BUTTON_ACTIVATION CONFIG+=APPLE_CARPLAY_PLUGIN_R16 CONFIG+=ENABLE_PLAYBACK_SERVICE CONFIG+=ENABLE_DYNAMIC_LOAD CONFIG+=ENABLE_AVM CONFIG+=ENABLE_BT_INIT_EARLY CONFIG+=ENABLE_KEY_SOUND CONFIG+=COMPILE_TEST -o Makefile avm_ui.pro
```

## make 1;make all 失效可能原因
* 在ssd_*/ 底下輸入
```bash=
source build/core/libcxx_env.sh CONFIG_ARM_AARCH64=y REG_LOGIN=n
```

## QT程式相關

### 簡單說明
* Headers 裡面存放標頭檔 .h 
* 標頭檔是用於 聲明class 還有 方法和變數，不需要在裡面實作
* 只要在.h有聲明的方法一定要在.cpp實作，不然編譯會出錯
* 例如我在.h裡面有
```cpp=
class A{
    public void funcA();
}
```
* 那我在.cpp 一定要實作
```cpp=
void A::funcA(){};
```
* 通常我會先在.h先寫類別後再到.cpp裡面實作
* connect 是QT很常用的方法 
```cpp=
connect(avmUiPNDMode->btnPNDTop2DFront,
SIGNAL(clicked()),
this,
SLOT(btnPNDTop2DFrontClicked()));
```
* 第一個參數是 發送信號的物件 `avmUiPNDMode->btnPNDTop2DFront`
* 第二個參數是 發送信號物件觸發的動作 `clicked()`
* 第三個參數是 接收信號的物件 `this`
* 第四個參數是 接收到信號後觸發的動作 `btnPNDTop2DFrontClicked()`
* `avmUiPNDMode->btnPNDTop2DFront` 被 `clicked()` 時 發送信號到 `this` 去觸發 `btnPNDTop2DFrontClicked()` 方法
### UI部分
* 在commons/comviews/basseview底下
* 需要繼承QWidget 也就是 `:public QWidget`
* AvmViewUi()建構子的`QWidget *parent=0` 就是從 `UIManager::getUIHerarchy(UIbottom)`來的
* 可以從 `new AvmViewWidget(UIManager::getUIHerarchy(UIbottom))`往下推
* 需要有 `Q_OBJECT` 才能用 signals slot
* 在.h先把物件聲明
```cpp=
class AvmViewUi:public QWidget
{
	Q_OBJECT

public:
	AvmViewUi(QWidget *parent=0);
	~AvmViewUi();

	QWidget* background;
	QLabel* labelBgImgMamBar;
	QLabel* labelBgMidLanguage;      //bg middle msg
	QLabel* labelBgRightLanguage;    //bg right msg

	elButton* btnSmod;
	elButton* btnChangeColor;

public slots:
	void btnChangeColorClicked();
	void btnSmodClicked();
};
```
* .cpp 部分是實做，把物件new出來
* 建構的時候傳參數到 AvmViewUi然後再傳到QWidget
* 因為是傳記憶體體位置，所以都是同一個QWidget 也就是 `UIManager::getUIHerarchy(UIbottom)`
* 剩餘部分參考程式碼註解
* QVariant 處理各種數據類型的通用容器
* QPixMap 設定圖片
```cpp=
AvmViewUi::AvmViewUi(QWidget *parent):QWidget(parent)
{
	AutoPrint p(LOG_TAG, __FUNCTION__);
	/***********************************************************************
	* 設定背景畫布
	************************************************************************/

    //在 UIManager::getUIHerarchy(UIbottom)之上建立子視窗 background
	background = new QWidget(this);
    //設定大小
	background->setGeometry(0,0,1280,800);
    //設定屬性 透明
	background->setAttribute(Qt::WA_TranslucentBackground);
    //顯示
	background->show();

	/***********************************************************************
	* 宣告新物件，設定作用區
	* 設定物件名稱
	* 設定區塊大小
	* 設定物件屬性
	************************************************************************/
    //new 一個Label物件出來 建立在background之上
	labelBgImgMamBar = new QLabel(background);
    //設定物件名稱
	labelBgImgMamBar->setObjectName(QString::fromUtf8("label_bg_img_mambar"));
    //設定物件大小
	labelBgImgMamBar->setGeometry(QRect(0,664,1280,136));

	btnSmod = new elButton(background);
	btnSmod ->setObjectName(QString::fromUtf8("btn_transparent"));
	btnSmod->setGeometry(QRect(1042,664,118,136));
    
    //設定哪個狀態要顯示哪張圖片
    //設定物件屬性，如果是normalBG則顯示圖片:/res/12_mam/btn/btn_setting_mod_n.jpg
	btnSmod->setProperty("normalBG",QVariant(QPixmap(QString::fromUtf8(":/res/12_mam/btn/btn_setting_mod_n.jpg"))));
    //設定物件屬性，如果是selectBG則顯示圖片:/res/12_mam/btn/btn_setting_mod_p.jpg"
	btnSmod->setProperty("selectBG",QVariant(QPixmap(QString::fromUtf8(":/res/12_mam/btn/btn_setting_mod_p.jpg"))));
	btnSmod->setProperty("disableBG",QVariant(QPixmap(QString::fromUtf8(":/res/12_mam/btn/btn_setting_mod_d.jpg"))));
	btnSmod->setProperty("disSelectBG",QVariant(QPixmap(QString::fromUtf8(":/res/12_mam/btn/btn_setting_mod_d.jpg"))));

    //如果btnSmod 被clicked() 就會觸發signals 到this的slot btnSmodClicked()
	connect(btnSmod,SIGNAL(clicked()),this,SLOT(btnSmodClicked()));
	connect(btnChangeColor,SIGNAL(clicked()),this,SLOT(btnChangeColorClicked()));
}
```
### PND畫面
* 檔案在activity底下avmactivity裡面的avmview資料夾

![](https://hackmd.io/_uploads/SycYNeDC2.png)
* 首先我們需要先建立一塊區域，在建構子的地方建立
```cpp=
AvmViewWidget::AvmViewWidget(QWidget *parent): BaseView("AvmViewWidget", parent)
{
	AVM_D(__FUNCTION__<<__LINE__<<"[start AVM activity]");

	setGeometry(UIManager::getMainAreaRect());

	avmView = new AVMView(UIManager::getDeskTopRect(), this);
	avmView->setVideoDisplayRect(UIManager::getMainAreaRect());
}
```
* AvmViewWidget 這個class 繼承BaseView
```cpp=
AvmViewWidget::AvmViewWidget(QWidget *parent): BaseView("AvmViewWidget", parent)
```
* 這樣的用法就是 我如果 new AvmViewWidget(parent)後，parent 會再傳到 BaseView的 parent裡面 去觸發BaseView 的建構子

* 建構子:冒號 後面可以直接初始化變數，像是這個例子初始化了 avmCtrl(想要初始化的值)等等 
```cpp=
AVMView::AVMView(QRect geometry, QWidget *parent):
QWidget(parent),avmCtrl(nullptr)
,avmUi(nullptr),avmUiPNDMode(nullptr)
```

* 程式流程
* 先 `new AvmViewWidget()` 然後再 `new AvmView()` 然後再 new 出 ui按鈕
* `new AvmViewWidget()` 繼承 `BaseView` 且QWidget是同一個物件，也就是說 `AvmViewWidget` 是 `BaseView` 的子視窗
* 主要程式碼都在`AvmVeiw` 也就是在 `avmview_p.h` 裡面的 class AvmView
* 為了校正模式的進入需要取的觸控點
* 觸控點需要先 `#include <QMouseEvent>` 才有辦法取得
* `AVMView::handleClick` 這個方法是抓四個點 排列正確就進入校正模式或其他模式
* `QMouseEvent` 的 pos() 可以取得 x y 座標
* 這個寫法可以知道 觸控點是否包含在 QRect的範圍內
* `QRect(0,0,160,83)` 的範圍
    1. x起始座標 
    2. y起始座標
    3. 從x起始座標往右多160pixel
    4. 從y起始座標向下多83pixel
```cpp=
QRect(0, 0, 160, 83).contains(event->pos())
```
* 其他方法沒有特別的地方 自行觀看即可
### R畫面
* 在commons/comviews/basseview底下
* 大致同PND畫面，想到特殊的再補

### AvmShare部分
* 存放共用的參數或是方法，沒有特別需要注意的
### 關機記憶 & 共用參數部分
* 在sysset/sysset/ 裡面的 elsysteminfo .cpp/.h
* .h部分
* `define QEL_AVM_JSON_DATA` 就是要存成`avm.jsondata`
```cpp=
#define QEL_AVM_JSON_DATA           "avm.jsondata"
#define AVM_LAST_PAGE               "avm.page"
#define AVM_CAR_COLOR               "avm.carColor"
#define AVM_MOD                     "avm.mod"
```
* 其餘三個就是要存 (或可以選擇不存) 的內容，存進json檔後會變成
```json=
avm.jsondata="@ByteArray({\"avm.carColor\":0,\"avm.mod\":0})"
```
* 結構體這樣寫就是 聲明一個結構體後直接給初始值
* 補充說明 `const avmInfo &avmData` 的寫法就是能避免`avmData`這個參數被修改 因為已經宣告他是 const (常數) 
* 其餘部分參考程式碼註解即可
```cpp=
struct avmInfo
{
	//non save
	EL_AVM_PAGE eCurPage;
	EL_AVM_MODE eCurMode;

	//save
	EL_AVM_BTN_INFO eCurMod;
	EL_AVM_CAR_COLOR eCarColor;

	avmInfo()
	{
		eCurMod.ON_OFF = EL_AVM_BTN_ON ;
		eCurMod.ENABLE_DISABLE = EL_AVM_BTN_ENABLE;
		eCarColor = EL_AVM_CAR_COLOR_RED;
		eCurPage = EL_AVM_PND_BACK;
		eCurMode = EL_AVM_PND_MODE;
	}
};
void AvmInfoToDefault(avmInfo &avmData);
void AvmInfoReadData(avmInfo &avmData);
void AvmInfoWriteData(const avmInfo &avmData);
```

### 封鎖觸控部分
* 在elhomeModuleImpl.cpp `slot_CBKKeyAction` 這個方法
* 校正部分要封鎖的動作 在`case KeyCamera` 這裡添加
* 同樣 avm的開關也是在此
```cpp=
else if(bAVM)
{
    if (ACTIVITY_AVM == curActivity)
    {
        ActivityManager::self()->stop(ACTIVITY_AVM);
    }
    else
    {
        selectActivity(ACTIVITY_AVM);
    }
}
```
### AVM是如何啟動 
* 同上面程式碼 追蹤到後面會觸發到 `elhomeModuleImpl::intoActivityUi`
```cpp=
if(ACTIVITY_CARPLAY == modeName || ACTIVITY_CAMERA == modeName
			|| ACTIVITY_AVM == modeName)
{
    emit sgn_disableUIButton();
    //這裡
    ActivityManager::self()->start(modeName, Temporary, subMode);
    if(ACTIVITY_CARPLAY == modeName)
    {
        BaseControl::getInstance()->sendUserEvent(MODULE_CARPLAY, USER_SET_CPACTIVITY_SUBTYPE, subMode);
        BaseControl::getInstance()->sendUserEvent(MODULE_CARPLAY, USER_SET_CPACTIVITYEVENT, true);
    }
}
```
* 然後觸發`ActivityManager::self()->start(modeName, Temporary, subMode);`

### 倒車切換 (還不確定正確性)
* 在commons/comviews/basseview底下
* 我自己添加了 開啟倒車時，要關閉PND；關閉導車時，要開啟 PND
```cpp=
void BaseControlPrivate::showAvmReverseView(bool en)
{

	if(en)
	{
		if(avmReverse.isNull())
		{
			avmReverse = new AvmReverseView(UIManager::getDeskTopRect(), UIManager::getUIHerarchy(UITop));
			if(avmReverse.isNull())
			{
				return;
			}
			ActivityManager::self()->stop(ACTIVITY_AVM);
			avmReverse->show();
		}
	}
	else
	{
		if(!avmReverse.isNull())
		{
			avmReverse->hide();
			delete avmReverse;
			ActivityManager::self()->start(ACTIVITY_AVM,Temporary);
		}
	}
}
```
### API 部分 IVI to KO
* 在apis/avmctrl底下
* 公有頭跟私有頭是設計模式的其中一種，之後熟悉再補上
* setPage部分
```cpp=
/*
* 先宣告一個 can_frame的結構體 `IVI_view_page`
* 裡面有id 跟 data
* avm_fd開檔
* if 做開檔失敗回傳error
* 第13行 IVI_view_page.data的起始位置，設為0，第三個參數是多少字節
* 第14行.can_id 參考AutoSys的protocal文件 200就是換頁功能
* 第15行.data[0]就是data的第0個位置設為 npage
* 第16行 ioctl 第一個參數是開啟的檔案 第二個是執行的動作 第三個是執行的值
*/

/*  AVM  IOCTL code  */
#define IVI_set_AVM_view_page 0x05
#define AVM_get_AVM_view_page 0x06

using namespace std;

#define AVMDEVPATH          "/dev/autosys_avm_cmd"
struct can_frame
{
	int  can_id;  /* 32 bit CAN_ID + EFF/RTR/ERR flags */
	int    data[10];
};

bool AvmControlPrivate::setAvmPage(int npage)
{
	CLOGD(__FUNCTION__<<npage);
	struct can_frame IVI_view_page;

	avm_fd = open(AVMDEVPATH, O_RDWR);
	printf("avm_fd = %d\n", avm_fd);
	if(avm_fd < 0)
	{
		CLOGD(__FUNCTION__ << "LINE:"<<__LINE__<<" Error: opening "<<AVMDEVPATH<<" fail!");
		return false;
	}
	memset(IVI_view_page.data, 0, sizeof(IVI_view_page.data));
	IVI_view_page.can_id = 0x200;
	IVI_view_page.data[0] = npage;
	ioctl(avm_fd, IVI_set_AVM_view_page, &IVI_view_page);

	close(avm_fd);
	return true;
}
```
* getPage
```cpp= 
int AvmControlPrivate::getAvmPage()
{

	struct can_frame IVI_view_page;
	int page = -1;
	avm_fd = open(AVMDEVPATH, O_RDWR);
	printf("avm_fd = %d\n", avm_fd);
	if(avm_fd < 0)
	{
		CLOGD(__FUNCTION__ << "LINE:"<<__LINE__<<" Error: opening "<<AVMDEVPATH<<" fail!");
		return page;
	}
	memset(IVI_view_page.data, 0, sizeof(IVI_view_page.data));

	ioctl(avm_fd, AVM_get_AVM_view_page, &IVI_view_page);

	page = IVI_view_page.data[0];

	close(avm_fd);
	CLOGD(__FUNCTION__<<page);
	return page;
}
```
* set是由ioctl將`IVI_view_page`資料傳進去，get是由ioctl吐資料回來到`IVI_view_page`