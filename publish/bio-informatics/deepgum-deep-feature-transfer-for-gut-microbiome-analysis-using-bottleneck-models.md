---
title: "DeepGum: Deep feature transfer for gut microbiome analysis using bottleneck models"
date: 2024-01-31
---

> Ref paper.
> https://www.sciencedirect.com/science/article/abs/pii/S1746809424000429

---

เปเปอร์นี้เสนอวิธีการใหม่ (DeepGum) ​โดย DeepGum เป็น Unsupervised โมเดลการถ่ายโอนคุณลักษณะเชิงลึก (Deep Feature Transfer) ที่พัฒนาขึ้นสำหรับการวิเคราะห์ microbiome ในลำไส้โดยใช้ Bottleneck Models เช่น Autoencoders และ U-nets เพื่อจัดการกับปัญหาขนาดตัวอย่างเล็กแต่มีมิติสูงในข้อมูลทางจุลชีววิทยาของลำไส้ โดยเน้นการปรับปรุงประสิทธิภาพในการจำแนกโรคจากข้อมูลทางจุลชีววิทยา

---
### Introduction

ในการวิจัยทางการแพทย์สมัยใหม่ ความสัมพันธ์ระหว่างระบบทางเดินอาหาร (Gastrointestinal, GI) และ microbiome ในลำไส้ได้รับความสนใจเป็นอย่างมาก เนื่องจากมีศักยภาพที่จะช่วยให้เกิดความเข้าใจเกี่ยวกับสุขภาพและโรคในมนุษย์ เช่น โรคลำไส้อักเสบเรื้อรัง (Inflammatory Bowel Disease, IBD) ซึ่งรวมถึงโรคโครห์น (Crohn’s disease, CD) และโรคลำไส้ใหญ่อักเสบเป็นแผลเรื้อรัง (Ulcerative Colitis, UC)

> ปัจจุบัน การวินิจฉัยทางคลินิกสำหรับ IBD รวมถึงการตรวจเลือด การส่องกล้องลำไส้ใหญ่ และ MRI อย่างไรก็ตาม เนื่องจากอาการที่ไม่ชัดเจน ทำให้มีการวินิจฉัยผิดพลาดได้บ่อยในทางปฏิบัติ​

> สำหรับโรคมะเร็งลำไส้ใหญ่ (Colorectal Cancer, CRC) ซึ่งเป็นโรคในระบบทางเดินอาหารที่มีความร้ายแรง CRC เป็นสาเหตุอันดับสามของการเกิดโรคมะเร็งใหม่ และเป็นอันดับสี่ในสาเหตุการเสียชีวิตทั่วโลก การพัฒนา CRC มีความสัมพันธ์กับปัจจัยหลายอย่าง เช่น พันธุกรรม อาหาร และวิถีชีวิตที่ไม่เหมาะสม​

การวิเคราะห์ microbiome ในลำไส้จึงถือเป็นวิธีที่มีแนวโน้มในการตรวจวินิจฉัยโรคในระบบทางเดินอาหารโดยไม่ต้องผ่าตัดและสะดวกยิ่งขึ้น เนื่องจากสามารถเก็บตัวอย่างจากบ้านโดยใช้ชุดทดสอบที่บ้านได้ การศึกษาเกี่ยวกับ microbiome ได้แสดงให้เห็นว่า microbiome มีบทบาทสำคัญในสุขภาพมนุษย์ และมีความสัมพันธ์กับโรคหลายชนิด การใช้การวิเคราะห์ microbiome สามารถเป็นวิธีที่มีประสิทธิภาพในการตรวจหาความผิดปกติในลำไส้​

---
### Materials and Methods

#### 1. Datasets and Pre-processing

เปเปอร์ใช้ 9 ชุดข้อมูล microbiome (9 different datasets related to [gastrointestinal diseases](https://www-sciencedirect-com.chula.idm.oclc.org/topics/medicine-and-dentistry/gastrointestinal-disease "Learn more about gastrointestinal diseases from ScienceDirect's AI-generated Topic Pages") from the ENA molecular database) 

>5 datasets comprise of [IBD](https://www-sciencedirect-com.chula.idm.oclc.org/topics/medicine-and-dentistry/inflammatory-bowel-disease "Learn more about IBD from ScienceDirect's AI-generated Topic Pages") cohorts and 4 datasets involve [CRC](https://www-sciencedirect-com.chula.idm.oclc.org/topics/pharmacology-toxicology-and-pharmaceutical-science/colorectal-cancer "Learn more about CRC from ScienceDirect's AI-generated Topic Pages") cohorts

ในลำไส้ที่เกี่ยวกับโรคในระบบทางเดินอาหาร ซึ่งประกอบไปด้วยชุดข้อมูลเกี่ยวกับโรคลำไส้อักเสบ (IBD) และมะเร็งลำไส้ใหญ่ (CRC) โดยใช้ **MetaPhlAn 3.0** ในการสกัด taxonomic features จากชุดข้อมูลและรวมเป็นชุดข้อมูลที่มีทั้งหมด 2377 ตัวอย่าง และ 1104 taxonomic features (merged dataset, with 1104 features and 2377 samples, is divided into 20% [test data](https://www-sciencedirect-com.chula.idm.oclc.org/topics/mathematics/test-data "Learn more about test data from ScienceDirect's AI-generated Topic Pages") and 80% training data.)

#### 2. Deep Feature Extraction with Unsupervised Methods

> โมเดลที่ใช้ใน DeepGum ประกอบด้วย Auto encoders และ U-nets ซึ่งทำหน้าที่ลดขนาดมิติของข้อมูลผ่านการบีบอัดข้อมูลลงใน latent space (พื้นที่แฝง) เพื่อสร้างตัวแทนข้อมูลที่มีประสิทธิภาพ 

ในส่วนนี้เน้นถึงการใช้วิธีการสกัดคุณลักษณะ (Feature Extraction)  ซึ่งเป็น Unsupervised Learning โดยใช้ **Autoencoders** และ **U-nets** เพื่อลดขนาดมิติของข้อมูลและสร้างตัวแทนข้อมูล (Latent Space) ที่สามารถใช้ในการจำแนกโรคได้ แม้ว่าจะมีขนาดข้อมูลตัวอย่างที่เล็ก โมเดลที่สกัดคุณลักษณะเหล่านี้จะช่วยให้ระบบสามารถจัดการกับข้อมูลที่มีมิติสูงได้อย่างมีประสิทธิภาพ
#### Auto encoders

Auto encoder เป็นสถาปัตยกรรมแบบ Neural Network ที่ประกอบไปด้วยสองส่วนหลัก คือ **Encoder** และ **Decoder** โดย:

- **Encoder** จะบีบอัดข้อมูล (Compress) เข้าไปในพื้นที่แฝง (Latent Space) ที่มีขนาดเล็กลง เพื่อลดขนาดมิติของข้อมูล
- **Decoder** จะทำการขยายข้อมูลกลับมาใหม่ เพื่อให้ได้ข้อมูลที่คล้ายกับข้อมูลต้นฉบับมากที่สุด

จากนั้น ข้อมูลที่บีบอัดจาก Encoder หรือข้อมูลที่สร้างใหม่จาก Decoder จะถูกส่งผ่านไปยังโมเดลจำแนก เช่น **MLP** (Multi-layer Perceptron), **RF** (Random Forest), หรือ **XGBoost** เพื่อใช้ในการทำนายหรือตรวจสอบโรคต่างๆ แม้ข้อมูลจะมีจำนวนตัวอย่างน้อยก็ตาม

#### U-nets

**U-net** เป็นสถาปัตยกรรม Neural Network ที่ออกแบบมาเพื่อการแบ่งส่วนภาพ (Image Segmentation) ในกรณีนี้ U-net ถูกใช้ในการสกัดคุณลักษณะเชิงลึกจากข้อมูลทางไมโครไบโอม โดย U-net จะลดมิติของข้อมูลผ่าน Encoder แล้วขยายกลับมาผ่าน Decoder เช่นเดียวกับ Autoencoder ข้อมูลที่ถูกลดมิติผ่าน U-net สามารถใช้ในการทำนายโรคได้โดยโมเดลจำแนกเช่นเดียวกัน

#### The overview of DeepGum method.
โครงสร้างและกระบวนการทำงานของโมเดล Autoencoder และ U-net ที่ถูกใช้ในงานวิจัยนี้สำหรับการจำแนกโรคจากข้อมูล mircobiome ซึ่งมีการแบ่งออกเป็น 4 ส่วนหลัก โดยแต่ละส่วนมีรายละเอียดดังนี้

![[Frame 2.png]]

> The overview of DeepGum method. This approach involves training an autoencoder or a U-net with the merged microbiome dataset. The encoder model or reconstructed model, which emerges through training, reused for datasets with low sample sizes. Then a [classifier model](https://www-sciencedirect-com.chula.idm.oclc.org/topics/mathematics/classifier-model "Learn more about classifier model from ScienceDirect's AI-generated Topic Pages") (MLP, XGB and RF) is utilized to determine whether the samples belong to patients or healthy controls.
### Layer details of DeepGum.

โดยแต่ละส่วนใน DeepGum แสดงให้เห็นการใช้งานของโครงสร้าง **U-net** และ **Autoencoder** สำหรับการสกัดคุณลักษณะเชิงลึกในข้อมูลไมโครไบโอม โดย **U-net1/4** และ **Autoencoder1/4** มีโครงสร้างครบทั้งการบีบอัดและการสร้างข้อมูลใหม่ ขณะที่ **U-net-encode1/4** และ **Encoder1/4** ใช้เฉพาะการบีบอัดข้อมูลเพื่อเลือกคุณลักษณะที่สำคัญ

![[Pasted image 20240922133557.png]]
#### 1. U-net1/4 (ส่วนซ้ายสุด)

- เป็นโครงสร้างของ **U-net** ซึ่งประกอบด้วยการบีบอัดข้อมูล (down-sampling) และการสร้างข้อมูลใหม่ (up-sampling)
- ในส่วนของการบีบอัด ข้อมูลนำเข้าจะถูกส่งผ่านเลเยอร์ **Conv1D** หลายชั้นเพื่อทำการคัดเลือกคุณลักษณะเชิงลึก
- แต่ละชั้นจะมีการลดขนาดข้อมูลด้วย **MaxPooling1D** เพื่อลดขนาดของข้อมูลที่ผ่านไปแต่ละขั้น
- หลังจากนั้นจะเข้าสู่กระบวนการสร้างข้อมูลใหม่ (up-sampling) ผ่านเลเยอร์ **UpSampling1D** ที่ขยายขนาดข้อมูลกลับมาในลักษณะเดิม
- ข้อมูลในชั้นสุดท้ายจะถูกประกอบใหม่ (concatenate) จากหลายช่องทางข้อมูล

#### 2. U-net-encode1/4 (ขวาของ U-net1/4)

- เป็นการใช้โครงสร้างของ **U-net** แต่ทำการบีบอัดข้อมูลเท่านั้นโดยไม่ทำการสร้างใหม่
- ข้อมูลจะถูกส่งผ่านเลเยอร์ **Conv1D** และ **MaxPooling1D** เหมือนกับโครงสร้าง U-net แต่จบที่การบีบอัดเพื่อนำคุณลักษณะที่ถูกเลือกไปใช้ในการจำแนกโรคในขั้นตอนต่อไป

#### 3. Encoder1/4 (กลางขวา)

- เป็นส่วนของ **Autoencoder** ที่ใช้เพียงโครงสร้างของ **Encoder**
- ข้อมูลจะถูกบีบอัดผ่านเลเยอร์ **Conv1D** และ **MaxPooling1D** หลายชั้นเพื่อสร้างตัวแทนข้อมูลที่มีขนาดเล็กลง
- โครงสร้างนี้ช่วยในการลดข้อมูลให้เหลือเฉพาะคุณลักษณะที่สำคัญสำหรับการจำแนกข้อมูล

#### 4. Autoencoder1/4 (ขวาสุด)

- โครงสร้างของ **Autoencoder** ที่ครบถ้วนทั้งการบีบอัดและการสร้างข้อมูลใหม่
- ข้อมูลจะถูกบีบอัดผ่าน **Conv1D** และ **MaxPooling1D** หลายชั้น จากนั้นขยายขนาดข้อมูลกลับมาด้วย **UpSampling1D**
- โครงสร้างนี้ใช้ในการสร้างข้อมูลใหม่ที่สูญเสียข้อมูลน้อยที่สุดจากข้อมูลที่ถูกบีบอัด

---
### Results

#### 1. ประสิทธิภาพของ DeepGum กับชุดข้อมูลทางเดินอาหาร (DeepGum on Gastro Datasets)

> DeepGum สามารถเพิ่มความแม่นยำในการจำแนกโรคได้เป็นอย่างมาก ไม่ว่าจะเป็นโรคลำไส้อักเสบเรื้อรัง (IBD) หรือมะเร็งลำไส้ใหญ่ (CRC) โดยเฉพาะเมื่อใช้การถ่ายโอนคุณลักษณะจาก Encoder ของ Autoencoder และ U-net ทำให้โมเดลสามารถปรับตัวได้ดีแม้ในกรณีที่มีข้อมูลตัวอย่างน้อย

ในส่วนนี้เน้นการวิเคราะห์ผลการทดลองของ **DeepGum** กับชุดข้อมูลที่เกี่ยวข้องกับโรคในระบบทางเดินอาหาร ซึ่งได้แก่โรคลำไส้อักเสบเรื้อรัง (IBD) และมะเร็งลำไส้ใหญ่ (CRC) เปรียบเทียบกับโมเดลพื้นฐานต่างๆ เช่น **MLP** (Multi-Layer Perceptron), **Random Forest (RF)**, และ **XGBoost** โดยทำการทดลองทั้งกับข้อมูลที่ถูกสร้างใหม่ (Reconstructed Data) และข้อมูลที่บีบอัดมาจาก **Encoder** ของ **Autoencoders** และ **U-nets**

- สำหรับชุดข้อมูล **IBD**:
    - DeepGum แสดงประสิทธิภาพที่เหนือกว่าโมเดลพื้นฐานในหลายกรณี โดยเฉพาะเมื่อใช้ข้อมูลที่ผ่าน Encoder ของ Autoencoder ซึ่งมีการแปลงข้อมูลอย่างมีประสิทธิภาพและสามารถช่วยในการแยกแยะระหว่างผู้ป่วยที่มี IBD กับผู้ที่ไม่เป็น ได้อย่างแม่นยำ
    - โมเดล **MLP** และ **XGBoost** ที่ใช้ข้อมูลจาก Encoder ของ U-net สามารถทำงานได้ดีขึ้นกว่าการใช้ข้อมูลต้นฉบับ
- สำหรับชุดข้อมูล **CRC**:
    - DeepGum ยังคงแสดงผลลัพธ์ที่เหนือกว่ามากเมื่อใช้ข้อมูลจาก Encoder ของ U-net และ Autoencoder ซึ่งช่วยเพิ่มความแม่นยำในการแยกแยะผู้ป่วยที่มีมะเร็งลำไส้ใหญ่จากกลุ่มตัวอย่างที่ไม่เป็นโรค
    - ทั้งนี้ ชุดข้อมูลที่ถูกลดขนาดลงยังแสดงให้เห็นถึงความสามารถของ DeepGum ในการจัดการกับข้อมูลที่มีจำนวนตัวอย่างน้อยและยังคงให้ผลลัพธ์ที่น่าพึงพอใจ

#### The DeepGum models that exhibit the best performance for each dataset are highlighted in red
![[Pasted image 20240921194911.png]]
> GI diseases prediction performance of DeepGum. For IBD-I, IBD-II, IBD-III, CRC-I, CRC-II and CRC-III datasets, the comparative classification results of RF, XGB, MLP, metaML and DeepMicro models and autoencoder and U-net based DeepGum models are given. Each value represents the average AUC score of five experiments.

#### Autoencoder and U-net models are evaluated for each dataset, and the outcomes are presented in
![[Pasted image 20240922134950.png]]
> GI diseases prediction comparative performance scores (AUC) for reconstructed and encoder models of U-net and autoencoder.
#### 2. กลุ่มตัวอย่างที่ถูกสุ่มลดขนาด (Randomly Downsampled Cohorts)

>DeepGum มีความสามารถในการรักษาประสิทธิภาพได้ดีแม้ในกรณีที่จำนวนตัวอย่างถูกลดลงอย่างมาก ซึ่งเป็นประโยชน์อย่างยิ่งในการวิเคราะห์ข้อมูล microbiome ที่มักประสบปัญหาการขาดแคลนข้อมูลตัวอย่าง ด้วยการใช้ Encoder ของ Autoencoder และ U-net ช่วยในการสร้างตัวแทนข้อมูลที่มีคุณภาพ แม้ข้อมูลต้นฉบับจะถูกลดจำนวนลง

ส่วนนี้ของการวิจัยเน้นการทดลองเกี่ยวกับการลดขนาดกลุ่มตัวอย่าง (Downsampling) **เพื่อทดสอบประสิทธิภาพของโมเดล DeepGum เมื่อใช้ข้อมูลที่มีจำนวนตัวอย่างน้อย** โดยการสุ่มลดขนาดของกลุ่มตัวอย่างในชุดข้อมูลที่ใช้ในการฝึกโมเดลให้มีขนาดเล็กลงอย่างมาก เพื่อจำลองสถานการณ์ที่ข้อมูลมีจำนวนไม่เพียงพอ ซึ่งเป็นปัญหาที่พบได้ทั่วไปในงานวิจัยทางด้าน microbiome

> ในการทดลองนี้ได้ทำการสุ่มลดจำนวนตัวอย่างของกลุ่มผู้ป่วยจากทั้งสองกลุ่มข้อมูล: **กลุ่มข้อมูล IBD** (โรคลำไส้อักเสบเรื้อรัง) และ **กลุ่มข้อมูล CRC** (มะเร็งลำไส้ใหญ่)

การสุ่มลดขนาดกลุ่มตัวอย่างมีวัตถุประสงค์เพื่อตรวจสอบว่า DeepGum สามารถรับมือกับสถานการณ์ที่มีข้อมูลน้อยได้ดีเพียงใด โดยเปรียบเทียบกับโมเดลพื้นฐาน เช่น **MLP** (Multi-Layer Perceptron), **Random Forest (RF)**, และ **XGBoost** ผลการทดลองแสดงให้เห็นว่า:

>**ประสิทธิภาพของโมเดลเมื่อกลุ่มตัวอย่างถูกลดขนาด**:
    - ในกลุ่มตัวอย่างที่ถูกลดขนาดลง โมเดลพื้นฐานส่วนใหญ่จะมีประสิทธิภาพที่ลดลงอย่างมาก เนื่องจากการเรียนรู้จากข้อมูลที่มีขนาดน้อยลงมักจะมีปัญหาเรื่องการแยกแยะคุณลักษณะที่สำคัญไม่เพียงพอ
    - ในทางกลับกัน **DeepGum** ยังคงรักษาประสิทธิภาพในการจำแนกโรคไว้ได้ดี เนื่องจากการใช้คุณลักษณะที่ถูกบีบอัดจาก **Encoder** ของ Autoencoder และ U-net ช่วยให้สามารถเก็บรักษาข้อมูลสำคัญและมีความแม่นยำสูงขึ้นในการจำแนก แม้ข้อมูลจะถูกลดขนาดลงอย่างมาก

> **การเปรียบเทียบประสิทธิภาพ**:
    - ผลการทดลองแสดงให้เห็นว่า DeepGum สามารถทำงานได้ดีกว่าโมเดลพื้นฐานอย่างมีนัยสำคัญ โดยเฉพาะอย่างยิ่งในกรณีที่จำนวนตัวอย่างมีน้อย ซึ่งเป็นข้อพิสูจน์ว่าโมเดลนี้สามารถรับมือกับปัญหาการจำแนกข้อมูลที่มีตัวอย่างจำกัดได้ดีกว่าโมเดลที่ไม่ได้ใช้วิธีการบีบอัดข้อมูล

![[Pasted image 20240922133458.png]]
> For 5, 10, 20, 50 and 100 randomly selected samples from each dataset, the comparative average AUC scores for RF classification and using the RF classifier after training with DeepGum models are given. (small-sample datasets namely IBD-I, IBD-II, IBD-III, CRC-I, CRC-II, and CRC-III.)

---
### Discussion

การศึกษาในเปเปอร์นี้ได้แสดงให้เห็นว่า **DeepGum** สามารถพัฒนาประสิทธิภาพในการจำแนกโรคจากข้อมูล microbiome ในลำไส้ได้ดีขึ้น แม้ว่าจะมีจำนวนตัวอย่างที่น้อยก็ตาม ซึ่งเป็นปัญหาสำคัญในการวิเคราะห์ข้อมูล microbiome 

โดย DeepGum ใช้ประโยชน์จากการถ่ายโอนคุณลักษณะที่สกัดมาจาก **Autoencoders** และ **U-nets** เพื่อสร้างตัวแทนข้อมูลที่มีประสิทธิภาพจากข้อมูลที่มีมิติสูง การใช้ **Unsupervised Learning** เพื่อบีบอัดข้อมูลใน latent space ทำให้สามารถเก็บรักษาข้อมูลสำคัญไว้ได้ โดยไม่สูญเสียข้อมูลมากเกินไป

#### ข้อดีของ DeepGum

- โมเดล DeepGum สามารถจัดการกับข้อมูลที่มีมิติสูงและขนาดตัวอย่างเล็กได้ดี ซึ่งมักเป็นปัญหาที่พบได้ทั่วไปในการวิเคราะห์ microbiome ในลำไส้
- การใช้ **Encoder** ของ Autoencoders และ U-nets ในการบีบอัดข้อมูลทำให้ได้คุณลักษณะที่มีความสำคัญในการจำแนกโรค ช่วยให้โมเดลพื้นฐาน เช่น **MLP**, **RF**, และ **XGBoost** ทำงานได้ดีขึ้นเมื่อเปรียบเทียบกับการใช้ข้อมูลต้นฉบับโดยตรง
- DeepGum ยังแสดงให้เห็นถึงประสิทธิภาพในการจำแนกที่สูง แม้ในกรณีที่มีการสุ่มตัวอย่างกลุ่มที่มีขนาดเล็ก (downsampled cohorts)

#### ความท้าทาย/ข้อจำกัด

- ขนาดของชุดข้อมูลที่ยังคงค่อนข้างเล็ก อาจเป็นข้อจำกัดในการนำไปประยุกต์ใช้ในกรณีอื่นๆ หรือในโรคที่มีความซับซ้อนมากขึ้น
- แม้ว่าโมเดล DeepGum จะแสดงผลลัพธ์ที่ดี แต่ยังคงต้องการการทดสอบเพิ่มเติมในชุดข้อมูลที่ใหญ่และมีความหลากหลายมากขึ้น เพื่อยืนยันความสามารถในการปรับตัวให้เข้ากับปัญหาต่างๆ ในข้อมูลไมโครไบโอม

---
### Conclusion and Future Work

การวิจัยนี้เสนอ **DeepGum** ซึ่งเป็นวิธีการสกัดคุณลักษณะเชิงลึกแบบไร้การกำกับที่มีประสิทธิภาพในการจัดการกับข้อมูลไมโครไบโอมที่มีมิติสูงและขนาดตัวอย่างเล็กได้ดี โดย DeepGum สามารถ Deep Feature Transfer ที่สกัดจาก Autoencoders และ U-nets เพื่อเพิ่มประสิทธิภาพในการจำแนกโรคที่เกี่ยวข้องกับระบบทางเดินอาหาร (IBD และ CRC) การทดลองแสดงให้เห็นว่าการใช้คุณลักษณะที่ได้จากโมเดล Encoder มีศักยภาพในการพัฒนาการจำแนกโรคที่แม่นยำกว่าโมเดลพื้นฐานเมื่อเทียบกับการใช้ข้อมูลต้นฉบับ
#### แนวทางในอนาคต

1. การทดสอบโมเดล DeepGum ในชุดข้อมูลที่มีขนาดใหญ่และหลากหลายมากขึ้น เพื่อทดสอบประสิทธิภาพในสถานการณ์ที่แตกต่างกัน
2. การปรับปรุงวิธีการเรียนรู้เชิงลึกเพิ่มเติม เช่น การใช้วิธีการเรียนรู้แบบกึ่งกำกับดูแล (Semi-supervised Learning) หรือการเรียนรู้ที่ใช้ความรู้ร่วมกัน (Transfer Learning) เพื่อเพิ่มความแม่นยำในการจำแนกโรคในชุดข้อมูลที่มีจำนวนตัวอย่างน้อย
3. การขยายการประยุกต์ใช้ DeepGum ไปยังข้อมูลไมโครไบโอมชนิดอื่นๆ และโรคอื่นๆ เพื่อทำให้แนวทางนี้สามารถครอบคลุมการวินิจฉัยโรคได้กว้างขวางยิ่งขึ้น