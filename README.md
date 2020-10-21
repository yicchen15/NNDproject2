# NND-project2
 
### 題目
A.
Training data 共有180張jpg圖片，共18樣商品，每張的尺寸為256\*256像素。
Validation data共有50張jpg圖片，共18樣商品，每張的尺寸為256\*256像素。
分類標籤與商品名的對照表請參考Product.txt或Product.xlsx。

B.  
jpg圖檔格式的linux愛用者請下載Training.tar+Validation.tar; windows愛用者請下載Training.zip+Validation.zip。
其分類標籤(label)請下載Training-label.txt+Valdation-label .txt; 喜愛用excel的同學可以下載Training-label.xlsx+Valdation-label.xlsx的格式

C.
Project2有兩部分，第一部分是程式佔80%，第二份是紙本Project佔20%。

程式部分的評分方式，老師會用另外50張商品圖做為測試集，你程式辨識出來的正確率即為程式部分的成績。
程式必須可以自動(不可手動按50次)分辨這新的50張圖，程式最後輸出為一個.txt,.csv,或.xlsx，其存有50X1的向量，代表此50個商品的分類。
紙本Project:需有簡介、流程、結論、心得、參考來源、程式碼。


------------------------------------------------
### Project

### 簡介

Training data 共有180張jpg圖片，共18樣商品，每張為256\*256 pixels組成，其label為180\*1之向量；Validation data共有50張jpg圖片，共18樣商品，每張的尺寸為256\*256 pixels，其label為50\*1之向量。將數據運用Keras建立模型並訓練。

### 流程
#### A.	參數設置 

epochs=50 (訓練50次)
batch_size=100 (將資料集任取90筆為一組)

#### B.	圖片影像處理
圖片原先為256pixels\*256pixels 先將圖片尺寸調整為128pixels\*128pixels，再放入模型進行訓練，驗證集也相同調整為128pixels\*128pixels；由於商品色彩也是一項重要資訊，所以保留RGB三通道。

#### C.	調整資料型態
將訓練集180張128pixels\*128pixels 合併為一個img_tra矩陣，以便訓練使用；label 轉為獨熱編碼，用來驗證輸出結果。將驗證集50張128pixels\*128pixels的圖片合併為一個umg_val的矩陣；label 也轉為獨熱編碼，用來驗證輸出結果。

#### D.	架構神經網路
建構卷積神經網路，選用Keras套件，架構依序2個隱藏層(包括卷積層與池化層)，再經過平滑層、ReLU激活函數，避免Overfitting 故Dropout 25%的特徵，最後輸出層以Softmax函數得到預測結果。運用cross_entropy來得到loss值再修正Weights和biases。訓練數據批量每次90筆，採隨機選取180筆中90筆，

### 結論
為縮短訓練時間、減少參數，故將圖片尺寸調整為128pixels\*128pixels。在第15次訓練準確率集達到9成，表示這神經網路已夠應付18類商品分類，不需再增加神經網路複雜度。
雖然訓練準確率達9成以上，但驗證集準確率只有4成左右，推斷可能與圖片顏色與背景有關係。

### 心得
對於模型的了解應更熟悉，在放入資料時較不容易產生錯誤，當準確率在經過幾次訓練達到一定準確率即可停止。
圖片為彩色，內容也較文字複雜，需訓練較多變數，但訓練集數目不多，且商品種類繁多，背景或是物品大小都會影響訓練與辨識。
訓練分類器時有較多圖片、且有變化越好，當資料不足時能夠使用旋轉、裁切、增加噪點等方式增加資料量，這次訓練準確率能夠達到99%故沒有額外增加資料。

---------------------------------------------------------
#### 10/5更新
project2-ImageDataGenerator.ipynb 
使用資料增量，想提高驗證時的準確率，但因圖片變多，訓練模型時CPU使用率100%怕操壞，所以試著使用GPU終於可行。

##### B1.增加訓練集 Data Augmentation
使用keras中的`ImageDataGenerator`，將圖片資料翻轉、平移、縮放，增加資料量與變化，並運用`im_new = batch[0]*0.97`稍微調整圖片像素值。

* https://medium.com/@CinnamonAITaiwan/cnn%E5%85%A5%E9%96%80-%E5%9C%96%E5%83%8F%E5%A2%9E%E5%BC%B7-fa654d36dafc
* https://medium.com/@shihaoticking/%E5%AF%A6%E4%BD%9C%E8%B3%87%E6%96%99%E5%BC%B7%E5%8C%96-data-augmentation-%E5%AF%A6%E7%8F%BE%E5%9C%96%E7%89%87%E7%BF%BB%E8%BD%89-%E5%B9%B3%E7%A7%BB-%E7%B8%AE%E6%94%BE-4b37d4400ffb
* https://hackmd.io/@allen108108/SyCsOIkxB


##### 使用GPU
參考以下資料：
* https://medium.com/@WhoYoung99/2018%E6%9C%80%E6%96%B0win10%E5%AE%89%E8%A3%9Dtensorflow-gpu-keras-8b3f8652509a
* 除錯用 https://stackoverflow.com/questions/60581677/attributeerror-module-tensorflow-core-api-v2-config-has-no-attribute-expe

##### 結果
原先訓練模型，每個epoch約需60s左右，運用GPU僅需2s左右，速度快約30倍。
透過資料增量(每一張再生3張)，訓練集從180張圖片增加到720張，且新圖片有調整像素值，避免與原先的圖片過於相似，最終驗證集(Validation)50張圖片，準確率達8成。
