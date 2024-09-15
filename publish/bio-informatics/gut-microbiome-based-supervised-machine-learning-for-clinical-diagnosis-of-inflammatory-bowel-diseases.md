---
title: Gut microbiome based supervised machine learning for clinical diagnosis of inflammatory bowel diseases
date: 2021-01-13
tags:
  - microbiome
  - ml
  - bioinformatics
---

> Ref paper.
> 
> https://www.ncbi.nlm.nih.gov/pmc/articles/PMC8828266/
> https://pubmed.ncbi.nlm.nih.gov/33439104/

---
> Inflammatory Bowel Disease (IBD) _เป็นกลุ่มโรคที่มีการอักเสบของระบบทางเดินอาหาร_

การตรวจวินิจฉัยโรคลำไส้อักเสบเรื้อรัง (IBD) หลายวิธี แต่ปัญหาการวินิจฉัยผิดพลาดยังคงเป็นเรื่องที่พบได้บ่อยในคลินิก ซึ่งทำให้เกิดความจำเป็นในการปรับปรุงความถูกต้องในการวินิจฉัยให้ดียิ่งขึ้น 

มีรายงานว่าเกิดความไม่สมดุลของจุลินทรีย์ในลำไส้ในผู้ป่วย IBD จึงมีการตั้งสมมติฐานว่าการเรียนรู้ของ Supervised Machine Learning อาจนำมาใช้ในการวิเคราะห์ข้อมูลจุลินทรีย์ในลำไส้เพื่อการวินิจฉัยที่แม่นยำขึ้นได้ 

เพื่อทดสอบสมมติฐานนี้ ได้ทำการวิเคราะห์ข้อมูลเมตาเจนอมิกส์ 16S จากตัวอย่างอุจจาระของผู้ป่วย IBD จำนวน 729 คน และผู้ที่ไม่เป็น IBD จำนวน 700 คน จากโครงการ American Gut Project โดยใช้โมเดล ML จำนวน 5 อัลกอริธึม ได้แก่ Random Forest (RF), Decision Tree (DT), Elastic Net (EN), Support Vector Machine (SVM) และ Neural Networks (NN) 

โดยผลการศึกษาแสดงให้เห็นว่าแบคทีเรียที่มีความแตกต่างกันถึง 50 ชนิด (ตามการวิเคราะห์ LEfSe ที่มีค่า LDA มากกว่า 3) ระหว่างกลุ่มที่เป็น IBD และไม่เป็น IBD โดยโมเดล RF ที่ฝึกด้วยลักษณะอนุกรมวิธานของแบคทีเรียสามารถวินิจฉัย IBD ได้อย่างแม่นยำ โดยมีค่า AUC (พื้นที่ใต้เส้นโค้ง ROC) ประมาณ 0.80 

จากนั้นมีการทดสอบว่า OTU (Operational Taxonomic Units) ที่มีความแปรปรวนสูงสุด 500 ตัวอย่างจะสามารถใช้เป็นลักษณะสำหรับฝึกโมเดล ML ได้หรือไม่ ผลการทดสอบพบว่าค่า AUC เพิ่มขึ้นเป็น 0.82 สำหรับ RF สุดท้ายในการแยกแยะระหว่างโรค Crohn's disease (CD) และ Ulcerative colitis (UC) โมเดลที่ฝึกด้วยลักษณะอนุกรมวิธานที่แตกต่างกัน หรือ OTU ที่มีความแปรปรวนสูงสุด สามารถแยกแยะ CD กับ UC ได้ด้วยค่า AUC มากกว่า 0.90 ซึ่งแสดงให้เห็นถึงศักยภาพของ AI ผ่านการฝึก ML ด้วยข้อมูลจุลินทรีย์ในลำไส้สำหรับการวินิจฉัย IBD อย่างแม่นยำ

---
### MATERIALS AND METHODS:

![[Frame 1.png]]
#### Data Collection and Processing:

ใช้ 16S rRNA metagenomics ของตัวอย่างอุจจาระถูกเก็บจากโครงการ American Gut Project โดยใช้ Redbiom จากตัวอย่างทั้งหมด 19,978 ตัวอย่าง (สถานะเมื่อวันที่ 5 กุมภาพันธ์ 2020; Qiita study ID: 10317) 

มีตัวอย่างที่เก็บจากผู้ป่วย IBD จำนวน 934 ตัวอย่าง ซึ่งผู้ป่วยได้รับการวินิจฉัยโดยแพทย์ผู้เชี่ยวชาญ และมีตัวอย่างจากกลุ่มที่ไม่มี IBD จำนวน 19,044 ตัวอย่าง หลังจากคัดเลือกตัวอย่างกลุ่มที่ไม่มี IBD แบบสุ่มจำนวน 941 ตัวอย่างผ่านการสลับตัวอย่างทั้งหมด เราได้ทำการควบคุมคุณภาพโดยการกรองตัวอย่างที่มีความถี่รวมต่ำกว่า 10,000 ครั้ง โดยการใช้ซอฟต์แวร์ QIIME 2 (v. 2019.10) ตาราง OTU ถูกสร้างขึ้นจากไฟล์ BIOM ที่ถูกกรอง ผลที่ได้คือ OTU ทั้งหมด 71,199 OTU สำหรับการวิเคราะห์ระหว่าง IBD กับ Non-IBD และ 18,627 OTU สำหรับการวิเคราะห์ระหว่าง CD กับ UC สุดท้ายตัวอย่างจาก 729 คนที่เป็น IBD และ 700 คนที่ไม่เป็น IBD ถูกใช้ในการวิเคราะห์ นอกจากนี้เรายังใช้ตัวอย่างกลุ่มควบคุมย่อยที่ไม่มี IBD อีกสองชุดเพื่อการทดสอบโมเดล

#### Taxonomic Analysis:

การวิเคราะห์ด้วย QIIME 2 โดยใช้ตัวจำแนก Naive Bayes ที่ถูกฝึกมาก่อนแล้วจากฐานข้อมูล Greengenes (v. 13.8) ที่มีความคล้ายคลึงกันในระดับ 99% ในการจัดกลุ่ม OTU ต่อมาใช้การวิเคราะห์ LEfSe เพื่อหาลักษณะอนุกรมวิธานที่มีการแสดงออกแตกต่างกันอย่างมีนัยสำคัญ ซึ่งแสดงผลผ่านแผนภาพแท่งและแผนภาพ cladogram เพื่อแสดงความสัมพันธ์ทางวิวัฒนาการของจุลินทรีย์

#### Supervised Machine Learning:

ในการจำแนกกลุ่ม IBD และ non-IBD ใช้โมเดล ML 5 ประเภท ได้แก่ Random Forest (RF), Decision Tree (DT), Elastic Net (EN), Support Vector Machine (SVM) และ Neural Networks (NN) 

> โมเดลเหล่านี้ถูกฝึกด้วย OTU โดยใช้แพ็กเกจ `caret` ในภาษา R ข้อมูลถูกแบ่งออกเป็นชุดฝึกฝน (70%) และชุดทดสอบ (30%) และทำการวิเคราะห์แบบ cross-validation 10 ครั้งเพื่อประเมินประสิทธิภาพของโมเดล ในการลดความซับซ้อนของข้อมูลและความซับซ้อนของการคำนวณ ใช้ OTU ที่มีความแปรปรวนสูงสุด 500 ตัวอย่างในแต่ละการฝึกฝน

---
### RESULTS:

#### Differential Taxonomic Composition between the IBD and Non-IBD Groups:

องค์ประกอบของจุลินทรีย์ในลำไส้ของกลุ่มที่เป็น IBD และไม่เป็น IBD มีความแตกต่างกันอย่างชัดเจน จากการวิเคราะห์ LEfSe พบว่าแบคทีเรีย 50 สายพันธุ์แตกต่างกันระหว่างกลุ่ม IBD และ non-IBD (LDA score > 3.0) ในระดับไฟลัมพบว่า _Firmicutes_, _Verrucomicrobia_ และ _Actinobacteria_ มีปริมาณมากกว่าในกลุ่ม IBD ขณะที่ _Bacteroidetes_ พบมากกว่าในกลุ่มที่ไม่เป็น IBD นอกจากนี้ในระดับสกุลพบว่าแบคทีเรียสกุล _Lachnospira_, _Morganella_, _Coprococcus_ และ _Akkermansia_ มีปริมาณมากในกลุ่ม IBD ขณะที่ _Pseudomonas_ และ _Acinetobacter_ พบมากกว่าในกลุ่มที่ไม่เป็น IBD

#### Supervised ML Models Trained with Taxonomic Features for Classifying IBD and Non-IBD:

โมเดล ML ที่ถูกฝึกด้วยลักษณะอนุกรมวิธานสามารถจำแนกกลุ่ม IBD กับ non-IBD ได้อย่างมีประสิทธิภาพ โมเดล RF มีประสิทธิภาพดีที่สุด โดยมีค่า AUC ที่ 0.80 และมีความแม่นยำ (accuracy) 72% ในการทดสอบ ในขณะที่โมเดลอื่นๆ เช่น EN, NN, SVM, และ DT มีประสิทธิภาพน้อยกว่าเล็กน้อย

![[Pasted image 20240913170153.png]]
> Performance measures of supervised machine learning models for classifying subjects with inflammatory bowel disease (IBD) and those without IBD (non-IBD) using gut microbiome features. Differential taxonomic features: area under the receiver operating characteristic curve (AUC) (_A_) and accuracy (_B_); high-variance OTU features: AUC (_C_) and accuracy (_D_). 
#### Supervised ML Models Trained with High-Variance OTU Features for Classifying IBD and Non-IBD:

โมเดล ML ที่ฝึกด้วย OTU ที่มีความแปรปรวนสูงสุด 500 ตัวอย่างมีประสิทธิภาพดีขึ้นในบางโมเดล เช่น RF, EN และ SVM โดย RF มีค่า AUC สูงถึง 0.82 และความแม่นยำเพิ่มขึ้นเป็น 74% การใช้ชุดข้อมูลที่แตกต่างกันเพื่อทดสอบความแม่นยำยืนยันผลลัพธ์เดิมที่ RF เป็นโมเดลที่มีประสิทธิภาพดีที่สุด

#### Taxonomic Analysis and Supervised ML Modeling for Classifying CD and UC:

การจำแนกกลุ่มย่อยของ IBD ระหว่าง CD และ UC พบแบคทีเรียที่แตกต่างกันอย่างชัดเจน โดยมีการระบุแบคทีเรีย 117 สายพันธุ์ที่แตกต่างกันระหว่างสองกลุ่ม (LDA score > 3.0) โมเดล RF ที่ฝึกด้วยลักษณะอนุกรมวิธานและ OTU สามารถจำแนกกลุ่ม CD กับ UC ได้ด้วยค่า AUC มากกว่า 0.90 และความแม่นยำอยู่ที่ 83%

![[Pasted image 20240913170718.png]]