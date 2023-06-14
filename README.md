# PCTool報告(新)

---

## 報告內容
1. PCTool簡介
2. 注意事項
3. 操作流程
4. config檔位置、內容、用途

---

## PCTool五大功能按鈕&簡介
PCTool是用以製作3D車模以及3D環景圖的一套軟體，以下是他的四大功能
1. Camera Calibration：校正魚眼鏡頭
2. Off Line Calibration：用來設置場地空間、車輛大小、校正類型（Type7,10)，並取得ROI、特徵點和鏡頭在車輛的模擬位置
3. View Generation：調整各個景象的畫面如視角往左右偏移、碗型結構、混合區域的位置、陰影模糊等
4. 3D Car Mapping：製作3D車模，並結合View Generation的畫面
5. MultiCarBinary : 合併各色車模成一個多色車模.bin檔

---

## 1. Camera Calibration

### 注意事項 - 事前準備
* 內參的pattern 
    ![](https://hackmd.io/_uploads/rJ2V3roH3.png =450x350)
    - 要>=9*7的格子數
    - 棋盤必須由正方形組成
    - 正方形尺寸建議>20mm
    - 方塊和邊界之間的空白應至少有一個方塊寬，如h2，w2
    - 圖案尺寸 22mm*22mm A4大小
    - 每個鏡頭拍攝的照片數量要超過10張(棋盤放在左4+，中2，右4+)
    - 所以方塊必須清晰可見 
    - 棋盤必須位於相機矩陣的所有位置 
    - 輸入的圖片為 bmp 檔
    - 輸出文件為 ipm 檔
### 操作流程 - 事中 <a id="CameraCalibSetting"></a>
1. 輸入圖片組 
    ![](https://hackmd.io/_uploads/ryp9YQorh.png)

2. 自動識別輸入圖像中的角點
    ![](https://hackmd.io/_uploads/BJtJ9XoSn.png)

3. 使用檢測到的角點執行校正
    ![](https://hackmd.io/_uploads/HywxcQorn.png)

4. 查看畸變校正後的圖片，控制大小及位置不會影響之後產出的結果
    ![](https://hackmd.io/_uploads/B1Yj7c3B3.png)


5. 儲存Camera Calibration參數
    ![](https://hackmd.io/_uploads/rk6icXirh.png =30%x) ![](https://hackmd.io/_uploads/SySXNK2Hh.png =60%x)

6. 產出ipm
    ![](https://hackmd.io/_uploads/HJdYlq2Hh.png)
### 產出結果 - 事後
- 產出的ipm在camera資料夾裡面，複製四份後分別改名為front，left，right，rear.ipm供後續Off Line Calibration和View Generation使用
    ![](https://hackmd.io/_uploads/S1PiVchS2.png)

- config內容如下圖所示
    ![](https://hackmd.io/_uploads/r1UH8chSn.png =60%x)
    ![](https://hackmd.io/_uploads/BkmsEvsr3.png )

- 產出的bmp檔案在toolData/outImage中，每張圖片都會畫出角點
    ![](https://hackmd.io/_uploads/HJI4NwNLh.png)
    ![](https://hackmd.io/_uploads/S1ESVvVUn.png)



## 2. Off Line Calibration

### 注意事項 - 事前準備
- 需要知道場地及車輛的大小
- 用何種校正布，校正布大小
- camera資料夾內需含有前後左右四張放置校正布的bmp檔
- 檢查是否存在四個imp檔，內容須和Camera Calibration產出的一樣

### 操作流程 - 事中
1. 設置場地、車輛、校正布類型 <a id="setting"></a>
    ![](https://hackmd.io/_uploads/ByiOsjnr2.png)

2. 設置ROI範圍，每次設定方框後都要點選Apply，四個鏡頭結果都滿意後點選Save & Exit  <a id="setROI"></a>
    <font color = "red" size="4">**ROI方框盡可能包住整個校正布**</font>
    ![](https://hackmd.io/_uploads/SkIjHi2Bn.png)
    ![](https://hackmd.io/_uploads/BkxyUo2Sn.png)

3. 取得特徵點，每個ROI方框內3個，共計六個，注意特徵點必須在交界處，如紅圈所示
    ![](https://hackmd.io/_uploads/SkHeBuhrn.png)

4. 手動調整特徵點 <a id="featurePoints"></a>
    ![](https://hackmd.io/_uploads/B10nzYTr3.png)
    ![](https://hackmd.io/_uploads/SkmaKj3Sn.png)


5. 模擬鏡頭和特徵點位置，<font color="red" size="4">**注意左右鏡頭位置盡量在同一直線上**</font>
    ![](https://hackmd.io/_uploads/H1TvOu2S3.png)

6. 檢查校正後結果，校正布<font color="red" size="4">**大小越相近越好，並且要直**</font>
    ![](https://hackmd.io/_uploads/BJPc7VjSh.png)

7. 儲存Off Line Calibration的結果
    ![](https://hackmd.io/_uploads/B1G2mVsSh.png)

8. Close後儲存Off Lice Calibration參數
    ![](https://hackmd.io/_uploads/HJI0NtTB3.png)


### 產出結果 - 事後
- 產出的檔案
    1. camera資料夾裡(第七步按Save後的結果)
        ![](https://hackmd.io/_uploads/H1_6ji2Bn.png =60%x)
        1. 相機外參 emp檔(由Off Line Calibration生成) 
            <a id="reference1"></a>
            ![](https://hackmd.io/_uploads/HJeUCtpSn.png)
            * 傾斜 : 當Zc軸與世界軸Y平行，旋轉Xc軸，往上角度+，往下角度-
            * 旋轉 : 旋轉Yc軸，逆時針+，順時針-
            * 平移 : 當Zc軸與世界軸Y平行，旋轉Zc軸，左+， 右-
            [![](https://hackmd.io/_uploads/BkPfT9aBh.png)](#back1)

        2. 相機內參 ipm檔(由Camera Calibration生成，Off Line更改部分內容)
            * 同上面Camer Calibration產出的ipm檔，若在第一步Setting中勾選最下面方塊，則會改動ipm檔中的Principal Point參數 ![](https://hackmd.io/_uploads/r1ZQRqaB2.png)
        3. Off Line Calibration參數 ppm檔(由Off Line Calibration生成)
            * 參數說明
                ![](https://hackmd.io/_uploads/Sy-dajaS3.png)
                ![](https://hackmd.io/_uploads/rkT9ToaS2.png) 
    2. config資料夾裡(第8步按Close後的結果)
        1. OffLineCalibTool.cnf，除了名字和版本與ppm不一樣以外，其他幾乎一致
            ![](https://hackmd.io/_uploads/SJPqePNI3.png)

## 3. View Generation

### 注意事項 - 事前準備
* 檢查camera資料夾內是否含有
    1. camera.cnf(定義每個鏡頭的翻轉、內參檔ipm、外參檔epm、點陣圖bmp)
    2. 四個鏡頭的ipm(由CameraClib產出)
    3. 四個鏡頭的epm(由OffLineClib產出)
    4. 四個鏡頭的bmp(鏡頭截圖)
* 檢查config資料夾內是否含有 viewGenTool.cnf

### 操作流程 - 事中
1. 製作縫合圖 
    ![](https://hackmd.io/_uploads/ry6jodEUh.png)
2. 選擇視角
    ![](https://hackmd.io/_uploads/ryFRP06Ih.png)
    1. 選擇視角
    2. <font color="red" size=4>記得每次做完操作後都要按一下UpdateView確保視角的正確性</font>
    3. 調整完當前視角後記得儲存 Save View...
    4. 最後全部視角做完後再按 Save & Exit
3. 調整縫合畫面 <a id="makeSubView"></a>
    ![](https://hackmd.io/_uploads/SJY-WaTLn.png)

4. 較常用到的地方，如紅框所示
    ![](https://hackmd.io/_uploads/rJRNQT68n.png)
    - Virtual Camera
        - x: 調整畫面整體向左向右
        - y: 調整畫面整體向上向下
        - z: 調整畫面整體高低
        <a id="back1"></a>
        - tilt(x) **有3D畫面才能調**: 調整傾斜 <a href="#reference1">參考，點擊圖片可此處</a>
        - roll(y) **有3D畫面才能調**: 調整旋轉 
        - pan(z) **有3D畫面才能調**: 調整平移 
        - forcal length: 調整遠近
        - cx **有SD畫面才能調**: 調整相機中心視角向左向右
        - cy **有SD畫面才能調**: 調整相機中心視角向上向下

    - Blend <a id="blend"></a>
        ![](https://hackmd.io/_uploads/Byf8SCp83.png)

        - 除了有SD視角的都能調
        - Shadow Area通常等於車體大小或再稍微大一些 (黑底圖)
        - Shadow Imgae一定要比Shadow Area大 (灰底圖)
        - 調整Shadow Image後 在Shadow & car 區塊才能看到調整後結果(灰底圖)
    - Shadow & Car
        - Smooth Boundary :調整灰底圖邊緣模糊<font color="red" size=6>注意每次都要手動調整</font>他不會存在conf裡面，這個會影響到之後轉車模
5. 若要改變PGL顏色，位置或線條大小，則需到config資料夾選擇要改變的視角<a id="PGL"></a>
    ![](https://hackmd.io/_uploads/BJ9tGe1D2.png)

    ![](https://hackmd.io/_uploads/SymNGx1vn.png)

    更改完成後，選擇製作縫合圖->選擇視角->更新view 之後右側就能看到結果
    ![](https://hackmd.io/_uploads/H1Pz7gJDn.png)

6. 製作實際相機圖<a id="MakeCamVeiw"></a>
    ![](https://hackmd.io/_uploads/ry1J2RT8n.png)
    
7. 調整視角拼接<a id="MakeVeiwLayout"></a>
    ![](https://hackmd.io/_uploads/SJmTaRTLn.png)
    ![](https://hackmd.io/_uploads/Hk6j2RT82.png)
    - 選中的子視角會有紅框
    - 若要調整子視角一定要八的倍數且要>8
    - x: 向左向右
    - y: 向上向下
8. 結合輔助線
    ![](https://hackmd.io/_uploads/SkOaC0T8h.png)
9. 製作bin檔
    ![](https://hackmd.io/_uploads/HJv3HkCIn.png)
10. 結束並儲存
    ![](https://hackmd.io/_uploads/ByGRByRUn.png)



### 產出結果 - 事後
#### 1. Make Sub View 每個視角儲存後會生成一個資料夾
![](https://hackmd.io/_uploads/ry6jodEUh.png)
![](https://hackmd.io/_uploads/H1igPkCLn.png)

各個資料夾裡面包含
![](https://hackmd.io/_uploads/BJs_PJR82.png)
- Lut.cnf 裡面資訊
    ![](https://hackmd.io/_uploads/H1oK2108n.png)
- svm
    ![](https://hackmd.io/_uploads/rJwnhJCI3.png)
- pgl
    ![](https://hackmd.io/_uploads/Bk7ahyCUh.png)
#### 2. Make View Layout 會在layout資料夾產出viewLayout.cnf
![](https://hackmd.io/_uploads/SJmTaRTLn.png)
![](https://hackmd.io/_uploads/B1clAk08h.png)
- viewLayout.cnf 裡面資訊
![](https://hackmd.io/_uploads/rJyBegAI2.png)

#### 3. Convert PGL Color Format 會在viewData資料夾產生Palette，還有在各個視角資料夾內產出輔助線的png圖檔
![](https://hackmd.io/_uploads/SkOaC0T8h.png)
![](https://hackmd.io/_uploads/B1OrHiC83.png)
![](https://hackmd.io/_uploads/HkIhHjAIh.png)

#### 4. Make SVMConfig.bin 會在outBin資料夾產生SVMConfig.bin
![](https://hackmd.io/_uploads/HJdsLoAIh.png)

![](https://hackmd.io/_uploads/BJGOIoCUn.png)

#### 5. Make SVM binary會在outBin資料夾產生SVM資料夾裡面包含Nand資料夾和svm_lut_ftl.bin、svm_lut_nonftl.bin兩個檔案，還有outHeader資料夾裡生成viewmode_config.h給程式使用
![](https://hackmd.io/_uploads/S1ObDoRI3.png)
![](https://hackmd.io/_uploads/rk-5wiCIh.png)


![](https://hackmd.io/_uploads/H1Vw-CCLh.png)

#### 6. Make PGL_image binary會產生所有關於PGL的檔案
![](https://hackmd.io/_uploads/H1iB_oR8n.png)

* outBin資料夾會多一個PGL_image.bin
    ![](https://hackmd.io/_uploads/BkQZYsA83.png)
    
* viewData裡面會多四個PGL相關的檔案
    ![](https://hackmd.io/_uploads/ByQLtj0Ih.png)

    - viewData_PglImgList.txt 儲存PGL圖檔存放的位置
        ![](https://hackmd.io/_uploads/HkxqKs0Uh.png)

    - viewData_PglImgList_rle_info.txt 和 viewData_PglImgList_LUT.txt儲存關於LUT的資訊(之後程式會用到)
        ![](https://hackmd.io/_uploads/ryV0FiAIh.png)
        
* 每個view資料夾裡面的PGL會生成RLE.bin
    ![](https://hackmd.io/_uploads/BJQdb1Jwn.png)

* outHeader資料夾會多一個PGL_rsclist.h檔給程式使用
    ![](https://hackmd.io/_uploads/HJuWfCRL3.png)

#### 7. Close之後會在config資料夾生成viewGenTool.cnf
![](https://hackmd.io/_uploads/r18y4k1w3.png)
![](https://hackmd.io/_uploads/H16L41kP3.png)
![](https://hackmd.io/_uploads/Hyp3K11D2.png)

## 4. 3D Car Mapping
### 注意事項 - 事前準備
- 每個視角資料夾內需含有lut.cnf(由viewGeneration 產出)，SVM資料夾內需含有兩張圖檔
    ![](https://hackmd.io/_uploads/Sk_LiVeDh.png)

- car3DModel資料夾內包含Top、Other兩個資料夾
    other資料夾裡面有四個.mtl檔案(車輪1234)，這是給車模上屬性的，另外四個是.obj檔，就是車模圖
    ![](https://hackmd.io/_uploads/BkY9uWJP3.png =50%x)![](https://hackmd.io/_uploads/H1XE_ZJDn.png =50%x)
   
   top資料夾裡面只會有一個.mtl檔案，<font color="red" size =5>需要從other資料夾中copy tire1的.obj檔</font>,如下圖所示
    ![](https://hackmd.io/_uploads/r1qH8Z1vh.png)

- config資料夾內需包含
    ![](https://hackmd.io/_uploads/Sy4Xa-JP2.png)

    MakeCarBin.cnf資料
    ![](https://hackmd.io/_uploads/B1V_UbxD2.png)
    ![](https://hackmd.io/_uploads/r1v9yXlP3.png)

- 了解資料架構
    ![](https://hackmd.io/_uploads/HJu2z8ev3.png)


### 操作流程 - 事中 <a id="Make3Dcar"></a>
1. <font color="red" size =5>先選Top資料夾製作Top2D車模</font>
    ![](https://hackmd.io/_uploads/SkPphVlw2.png)
2. 顯示3D車模&調整
    ![](https://hackmd.io/_uploads/Bkzv6Exwn.png)
3. <font color="red" size =5>top資料夾只做這個畫面!</font>，因為top2D車模圖片有特別設計過，所以需要獨立拉出來製作
    ![](https://hackmd.io/_uploads/S1h7AEewn.png)
4. 重新載入車模檔案，選擇other資料夾
    ![](https://hackmd.io/_uploads/SkKnyBxvh.png)
5. 選擇車輪1，並顯示3D車模調整
    ![](https://hackmd.io/_uploads/HJ4fLLxDn.png)
6. 製作車子影像，<font color="red" size =4>注意!<font color="red" size =6>輪子1</font>的View List <font color="red" size =6>0.Top2D</font>不需要做</font>，其餘車輪2，3，4則正常操作，每個視角點下去後按Save Car Image...
    ![](https://hackmd.io/_uploads/ByUjvLxD2.png)
7. 車輪1每個視角做完後，點Convert Color Format
    ![](https://hackmd.io/_uploads/H1zckweD2.png)

8. 車輪1做完後，重新回Load 3D Car Model製作車輪2，3，4。步驟跟上面一樣。車輪2、3、4需要做Top2D
    ![](https://hackmd.io/_uploads/HyZCOIgw3.png)
    ![](https://hackmd.io/_uploads/Hyi8KLgPh.png)

9. 車輪2、3、4每個視角做完後，點Convert Color Format，<font color="red" size =5>記得點Existing</font>
    ![](https://hackmd.io/_uploads/B13f-weP3.png)

10. 車輪1、2、3、4都做完後點選Save Bin儲存
    ![](https://hackmd.io/_uploads/S1CibwxDh.png)

11. 若有大燈on/off或其他車色，先把產出的bin存到自建的資料夾內，也建議把viewData整個資料夾打包存到自建的資料夾內做備份 (如:白_有車燈、黑_有車燈等等) 再去轉其他車模顏色
    ![](https://hackmd.io/_uploads/B189Qwgv3.png)
    ![](https://hackmd.io/_uploads/BJTGXvxP3.png)

### 產出結果 - 事後
- 在Make Car Image... 點Save Car Image...會在視角資料夾裡面生成車子的圖檔
    ![](https://hackmd.io/_uploads/SyZyuwlDh.png)
    ![](https://hackmd.io/_uploads/HJ4DuDlPh.png)
- 在Convert Color Format點擊後 生成圖片+陰影的32位元圖檔之後再轉換成8位元圖檔，同時 /toolData/palette裡面的Bin檔也會被更新
    ![](https://hackmd.io/_uploads/HJJlVnLD2.png)
    ![](https://hackmd.io/_uploads/H14p9wgv2.png)

    /toolData/palette裡面的Bin檔
    ![](https://hackmd.io/_uploads/By0tE3IP3.png)

- 在Save Bin點擊後會在outBin資料夾生成car_image-1280*720.bin黨
    ![](https://hackmd.io/_uploads/BJTGXvxP3.png)

- 點擊Close後會更新config資料夾內的MakeCarBin.cnf
    ![](https://hackmd.io/_uploads/SkzmpPlvh.png)
    ![](https://hackmd.io/_uploads/Sy4Xa-JP2.png)

## 5. MultiCarBinary
### 注意事項 - 事前
* 需要第四步驟產出的各色車模bin檔
    ![](https://hackmd.io/_uploads/SkmnYAUD2.png)

### 操作流程<a id="MultiCar"></a>
* 在PixelPlus工具同一層的bin資料夾內有一個PI5008KSvmMultiCarBinary
    ![](https://hackmd.io/_uploads/Bk7F5CID3.png)
* 點開後按ADD加入各色車模bin檔
    ![](https://hackmd.io/_uploads/H1ZxoCLw3.png)
* Del 刪除選中一筆資料
* Clear 刪除全部資料
* 最後點擊 Make Multi Car_image.bin生成多色車模bin檔
    ![](https://hackmd.io/_uploads/SJ5r2CIw2.png)

### 產生結果
* 在outBin資料夾底下會生成一個car_image-multi.bin的檔案
    ![](https://hackmd.io/_uploads/HyeEO3AID3.png)

---

## PixelPlus工具設定檔
* PCTool參數檔，與PCTool在同一層，裡面可以設定路徑![](https://hackmd.io/_uploads/r1LVuapB2.png)
    設定檔內容:
    ![](https://hackmd.io/_uploads/rkaQknUw2.png)
* 製作多色車模功能

## 常用功能導航
### 1. CameraCalib
* <a href="#CameraCalibSetting">**相機校正**</a>
### 2. OffLineCalib
* <a href="#setting">**調整場地、車輛、校正布類型**</a>
* <a href="#setROI">**調整ROI範圍**</a>
* <a href="#featurePoints">**調整特徵點**</a>
### 3. ViewGeneration
* <a href="#makeSubView">**調整縫合畫面**</a>
* <a href="#blend">**調整混合區**</a>
* <a href="#PGL">**調整輔助線**</a>
* <a href="#MakeCamVeiw">**調整實際相機圖**</a>
* <a href="#MakeVeiwLayout">**調整視角拼接**</a>
### 4. 3DCarMapping
* <a href="#Make3Dcar">**製作車模**</a>
### 5. MultiCarBinary 
* <a href="#MultiCar">**合併各色車模**</a>

