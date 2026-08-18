# Python Data Pipeline Engineering Lab

## 🚀 วิธีการติดตั้งและใช้งาน (Usage)
1. อัปโหลดไฟล์ dataset: `Python_Data_Pipeline_Lab_Dataset (1).xlsx` ไปยัง **Google Colab**
2. รัน **Code Cell** ทั้งหมดตามลำดับตั้งแต่ **Cell 1 ถึง Cell 7**
3. ระบบจะทำการสร้างและส่งออกไฟล์ให้อัตโนมัติ จำนวน 3 ไฟล์:
   - `retail_dw.db`
   - `quarantine.csv`
   - `pipeline_run_log.csv`
4. คลิกดาวน์โหลดไฟล์ทั้ง 3 ไฟล์จากผลลัพธ์ของ Cell สุดท้ายเพื่อนำไปส่งงาน

---

## 🏗️ โครงสร้าง Star Schema (Data Warehouse)
โมเดลนี้ได้รับการออกแบบตามหลักการ **Star Schema** ซึ่งประกอบด้วย:

* **Grain ของ Fact Table:** 1 รายการขายสินค้าที่ผ่านการตรวจสอบข้อมูลเรียบร้อยแล้ว ต่อ 1 `order_id`
* **`dim_customer`**: เก็บข้อมูลลูกค้า
  * *Columns:* `customer_key`, `customer_id`, `customer_name`, `province`, `segment`
* **`dim_product`**: เก็บข้อมูลสินค้าที่มีสถานะ **Active** เท่านั้น
  * *Columns:* `product_key`, `product_id`, `product_name`, `category`
* **`dim_date`**: เก็บข้อมูลมิติเวลา
  * *Columns:* `date_key` (รูปแบบ YYYYMMDD), `full_date`, `day`, `month`, `quarter`, `year`
* **`fact_sales`**: เก็บข้อมูลธุรกรรมการขาย
  * มีการเชื่อม Foreign Key เข้ากับ Dimension ทั้ง 3 ตาราง
  * คำนวณค่า `gross_amount` และ `net_amount` เรียบร้อยแล้ว

---

## 🛠️ การจัดการคุณภาพข้อมูล (Data Quality Handling)

* **Type Conversion:** ใช้ `errors='coerce'` ในการแปลง Data Type เพื่อป้องกัน Error จากข้อความที่ไม่ใช่ตัวเลข (เช่น `"THB 979.4"`, `"three"`, `"*"` )
* **Referential Integrity:** ปฏิเสธ order ที่:
  * ไม่พบ `customer_id`
  * ไม่พบ `product_id`
  * สินค้ามีสถานะ `active_flag = 'N'`
* **Business Rules Validation:** 
  * `quantity > 0`
  * `unit_price > 0`
  * `0 <= discount_pct <= 100`
  * วันที่ต้องไม่ใช่ `NaT`
* **Text Normalization:** ปรับมาตรฐานข้อความ เช่น 
  * `"credit card"` $\rightarrow$ `"Credit Card"`
  * `"E-Commerce"` $\rightarrow$ `"Online"`
* **Deduplication:** กำจัดข้อมูลซ้ำ โดยเลือกเก็บ record ที่มี `updated_at` ล่าสุด

---

## 💡 Reflection: เหตุใด Availability จึงมักสำคัญกว่า Strictness ใน Production Pipeline?

ในระบบ Production จริง การที่ Pipeline หยุดทำงานทั้งหมด (**Strictness**) เพียงเพราะเจอข้อมูลสกปรกไม่กี่แถว จะส่งผลให้ Data Warehouse ไม่ได้รับข้อมูลใน Batch นั้นๆ ทำให้ Dashboard ของบริษัทขาดข้อมูลล่าสุด ซึ่งอาจกระทบต่อการตัดสินใจทางธุรกิจได้

การออกแบบ pipeline แบบ **Availability** จึงใช้หลักการ **Quarantine** โดยทำการแยกข้อมูลที่ผิดพลาดออกไปเก็บไว้ต่างหาก พร้อมบันทึกสาเหตุ (`reason_code`) แล้วปล่อยให้ข้อมูลที่สะอาดโหลดเข้าระบบต่อไปได้ 

วิธีนี้ทำให้ระบบมีความต่อเนื่อง (**Resilient**) และเปิดโอกาสให้ทีม Data Engineer สามารถย้อนกลับไปแก้ไข Root Cause ของข้อมูลสกปรกได้ในภายหลัง โดยที่การดำเนินงานทางธุรกิจยังคงดำเนินต่อไปได้โดยไม่หยุดชะงัก
