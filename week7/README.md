Python Data Pipeline Engineering Lab
วิธีติดตั้งและรัน
อัปโหลดไฟล์ Python_Data_Pipeline_Lab_Dataset (1).xlsx ไปยัง Google Colab
รัน Code Cell ทั้งหมดตามลำดับ (Cell 1 ถึง Cell 7)
ระบบจะสร้างไฟล์ retail_dw.db, quarantine.csv และ pipeline_run_log.csv ให้โดยอัตโนมัติ
คลิกดาวน์โหลดไฟล์ทั้ง 3 ไฟล์จากผลลัพธ์ของ Cell สุดท้ายเพื่อนำไปส่ง
โครงสร้าง Star Schema (Data Warehouse)
โมเดลนี้ออกแบบมาเป็น Star Schema ประกอบด้วย:

Grain ของ Fact Table: หนึ่งรายการขายสินค้าที่ผ่านการตรวจสอบข้อมูลเรียบร้อยแล้ว ต่อ 1 order_id
dim_customer: เก็บข้อมูลลูกค้า (customer_key, customer_id, customer_name, province, segment)
dim_product: เก็บข้อมูลสินค้าที่มีสถานะ Active เท่านั้น (product_key, product_id, product_name, category)
dim_date: เก็บข้อมูลมิติเวลา (date_key รูปแบบ YYYYMMDD, full_date, day, month, quarter, year)
fact_sales: เก็บข้อมูลธุรกรรมขาย มีการเชื่อม Foreign Key เข้ากับ Dimension ทั้ง 3 ตาราง พร้อมคำนวณ gross_amount และ net_amount แล้ว
การจัดการข้อมูล (Data Quality Handling)
ใช้ errors='coerce' ในการแปลง Type เพื่อป้องกัน Error จากข้อความที่ไม่ใช่ตัวเลข (เช่น "THB 979.4", "three", "*")
ตรวจสอบ Referential Integrity: ปฏิเสธ order ที่มี customer_id ไม่พบ, product_id ไม่พบ หรือสินค้า active_flag = 'N'
ตรวจสอบ Business Rules: quantity > 0, unit_price > 0, 0 <= discount_pct <= 100 และวันที่ต้องไม่ใช่ NaT
Normalize ค่า Text เช่น "credit card" -> "Credit Card", "E-Commerce" -> "Online"
Deduplicate ด้วยการเก็บ record ที่มี updated_at ล่าสุด
Reflection: เหตุใด Availability จึงมักสำคัญกว่า Strictness ใน Production Pipeline
ในระบบ Production จริง การที่ Pipeline หยุดทำงานทั้งหมด (Strictness) เพราะข้อมูลสกปรกเพียงไม่กี่แถว จะส่งผลให้ Data Warehouse ไม่ได้รับข้อมูล Batch นั้นๆ ทำให้ Dashboard ของบริษัทขาดข้อมูลล่าสุด และอาจกระทบต่อการตัดสินใจทางธุรกิจได้ การออกแบบแบบ Availability จึงใช้หลักการ Quarantine แยกข้อมูลที่ผิดพลาดออกไปเก็บไว้ต่างหากพร้อมบันทึกสาเหตุ (reason_code) แล้วปล่อยให้ข้อมูลสะอาดโหลดเข้าระบบต่อ ทำให้ระบบมีความต่อเนื่อง (Resilient) และทีม Data Engineer สามารถย้อนกลับไปแก้ไข Root Cause ของข้อมูลสกปรกได้ในภายหลังโดยที่ธุรกิจยังคงดำเนินต่อไปได้
