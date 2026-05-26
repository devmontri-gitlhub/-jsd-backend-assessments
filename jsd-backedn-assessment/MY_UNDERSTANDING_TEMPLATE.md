# My Understanding

## Submission Links

**Loom Video (must be set to public — anyone with the link):**
[paste your Loom video URL here]

---

## Questions

Answer each question in your own words. There are no trick questions.

The goal is not a perfect answer — it is an honest one. Write as if you are explaining to a friend who has never used Express. Completing this will prepare you for your video walkthrough.

Do not copy from documentation, your code comments, or AI output. If you are unsure about something, write what you do understand and note where the gap is.

---

**1. What does each HTTP method in your API mean — GET, POST, PUT or PATCH, and DELETE? Why do we use different methods instead of just using POST for everything?**

*Your answer:  จากไฟล์ REST Client ที่ผมได้สร้างไว้ ทั้ง 3 ไฟล์ (เช่น ไฟล์เพิ่มสินค้าตัวอย่าง 10 รายการ, การจัดการสินค้าในรูปแบบต่างๆ และการจัดการลบสินค้า) ทั้งหมดนี้มีการส่ง Request ไปยังไฟล์ index.js หลังบ้านผ่านช่องทางที่เรียกว่า HTTP 
     
     โดยภายใน HTTP จะเลือกใช้ Method (คำสั่ง) ที่ต่างกันตามการใช้งาน คือ GET, POST, PUT/PATCH, และ DELETE 
           • GET: ใช้สำหรับดึงข้อมูลสินค้ามาแสดงผล
           • POST: ใช้สำหรับเพิ่มหรือสร้างข้อมูลสินค้าใหม่
           • PUT / PATCH: ใช้สำหรับแก้ไขหรืออัปเดตข้อมูลสินค้า
           • DELETE: ใช้สำหรับลบข้อมูลสินค้า

     แต่ในทางเทคนิคสามารถใช้ Method POST ตัวเดียวเพื่อส่งข้อมูลไปทำทุกอย่างได้เลย (ทั้งดึง ทั้งแก้ไข ทั้งลบ) แต่ที่ไม่นิยมใช้ POST สำหรับทุกอย่าง มีหลายสาเหตุ เช่น: 

          1. ไม่ได้มาตรฐานสากล: การแยก Method ให้ตรงหน้าที่ (ตามมาตรฐาน RESTful) จะทำให้โค้ดอ่านง่าย ชัดเจน และทำงานร่วมกับ Frontend หรือคนอื่นๆ ในทีมได้ง่ายกว่า

          2. ความปลอดภัยและการทำงานของระบบ: Method อย่าง GET หรือ DELETE ถูกออกแบบมาให้ส่งซ้ำๆ ได้โดยข้อมูลไม่พัง (ปลอดภัยกว่า) แต่ถ้าเราใช้ POST ทำทุกอย่าง หากระบบรวนแล้วส่ง Request ซ้ำ อาจทำให้มีข้อมูลขยะหรือข้อมูลเบิ้ลใน Database ได้

          3. ประสิทธิภาพดีกว่าเพราะ Web Browser รองรับ และถูกออกแบบมาให้เข้าใจมาตรฐาน HTTP Method เช่น มันสามารถจำข้อมูล (Cache) จากคำสั่ง GET ได้ ทำให้ระบบโหลดข้อมูลฝั่ง Frontend เร็วขึ้นมาก ซึ่งถ้าใช้ POST ดึงข้อมูล Browser จะไม่สามารถทำงานข้อมูลของ Cache ให้"*

---

**2. What is `express.json()` and what would happen if you left it out?**

*Your answer:  express.json() คือ Middleware (ตัวกลาง) ของ Express.js ที่ทำหน้าที่ดักจับและแปลงข้อมูลที่ส่งมากับ HTTP Request ในรูปแบบ JSON ให้กลายเป็น JavaScript Object เพื่อให้ระบบ Backend สามารถนำไปประมวลผลต่อได้ทันที

   ยกตัวอย่าง : ในโปรเจกต์ของผม ตอนที่ยิงคำสั่ง POST หรือ PUT ผ่านไฟล์ REST Client เพื่อเพิ่มหรือแก้ไขสินค้า ผมมีการแนบชุดข้อมูล JSON ไปด้วย (เช่น {"name": "Gaming Mouse", "price": 1290}) ซึ่งตัว Method : express.json() ที่ผมตั้งค่าไว้ในไฟล์ index.js จะเป็นด่านหน้าที่คอยแปลง JSON ชุดนี้ ให้กลายเป็นข้อมูลที่ Controller สามารถเรียกใช้งานได้ผ่าน Property body ของ Object req

หากผมลืมใส่ หรือเอา express.json() ออก จะเกิดผลดังนี้ :

    1. ระบบจะอ่านข้อมูลไม่รู้เรื่อง: Backend ของผมจะไม่สามารถอ่านข้อมูล JSON ที่ Frontend หรือผู้ใช้งานส่งมาได้

    2. ค่ากลายเป็น Undefined เมื่อ Controller พยายามเรียกใช้ req.body เพื่อนำข้อมูลไปใช้งาน ค่าที่ระบบมองเห็นจะกลายเป็น undefined (ไม่มีค่า)  [หมายเหตุ : undefined มีความหมายว่า "ยังไม่ได้ถูกกำหนดค่า"]

    3. การบันทึกข้อมูลล้มเหลว เมื่อค่าเป็น undefined การสร้างสินค้าใหม่ (POST) หรืออัปเดตสินค้า (PUT) จะเกิด Error หรือถ้าระบบไม่ได้ป้องกันไว้ ก็อาจจะบันทึกค่าว่างเปล่า (Null) ลงไปในฐานข้อมูลแทนครับ แต่ถ้าเรามีการเขียนโค๊ดป้องกันเอาไว้ก็ช่วยเพิ่มความปลอดภัยได้ 

    นอกจาการใช้ express.js แล้ว เราอาจจะกลับไปใช้การเขียนแบบ Node.js ก็ได้ แต่ข้อเสียคือโค๊ดยาว และหากต้องมีการแก้ไขก็จะยุ่งยาก
 *

---

**3. What is the difference between `req.body`, `req.params`, and `req.query`? Give a real example from your API for each one.**

*Your answer: โดยทั้ง 3 ตัว นี้คือช่องทางที่ Backend (Express.js) ใช้รับข้อมูลที่ฝั่ง Client หรือ Frontend ส่งเข้ามา โดยจะถูกเลือกใช้งานในบริบทที่แตกต่างกัน ดังนี้ : 

    1. req.body (ส่งข้อมูลเป็นก้อน Payload)
         การทำงาน: ใช้สำหรับรับข้อมูลที่มีความซับซ้อน เป็นความลับ หรือมีขนาดใหญ่ (มักส่งมาในรูปแบบ JSON) โดยข้อมูลจะถูกซ่อนมาใน Body ของ HTTP Request มักใช้กับ Method POST และ PUT (วิธีการส่งแบบนี้มีความปลอดภัยที่สูง)
        
        ตัวอย่าง : จาก API ของผม ตอนที่ผมต้องการเพิ่มสินค้าใหม่ ผมส่ง Method POST พร้อมแนบ JSON ไปว่า {"name": "Gaming Mouse", "price": 1290} ซึ่งในไฟล์ Controller ของผมจะอ่านค่านี้ผ่าน req.body.name และจัดการ จากนั้นโยนก้อน req.body ทั้งหมดเข้าไปไฟล์ Model เพื่อตรวจสอบ หากถูกต้องก็จะโยนไปบันทึกใน MongoDB เพื่อจัดเก็บข้อมูลที่ถูกส่งมา

    2. req.params (ส่งข้อมูลระบุตัวตนใน URL Path)
         การทำงาน: ใช้รับข้อมูลที่ถูกฝังมาเป็นส่วนหนึ่งของโครงสร้าง URL (Endpoint) พร้อมกัน มักใช้สำหรับการระบุ "ตัวตน" ของข้อมูลที่ต้องการจัดการแบบเจาะจง เช่น ค่า ID (วิธีการส่งนี้ไม่เหมาะสำหรับส่งข้อมูลที่เป็นความลับ)

         ตัวอย่าง: จาก API ของผม ในไฟล์ Route ผมกำหนด Path ไว้ว่า router.delete('/:id') ดังนั้นเมื่อมี Request ส่งมาเพื่อลบสินค้าที่ URL DELETE /api/products/662b1... ตัว Backend จะสามารถดูดเอาไอดี 662b1... ไปใช้งานต่อได้ผ่านคำสั่ง req.params.id [หมายเหตุ : ขึ้นตอน หรือการเขียนโค๊ดก็จะคล้ายๆ กันกับตัวอย่าง ของ req.body]

    3. req.query (ส่งข้อมูลต่อท้าย URL เพื่อค้นหา/กรอง)
         การทำงาน: ใช้รับข้อมูลที่แนบมาท้าย URL โดยมีเครื่องหมายคำถาม ? นำหน้า และใช้ & เชื่อม (Query String) มักถูกนำมาใช้สำหรับการทำระบบค้นหา (Search), กรองข้อมูล (Filter) หรือแบ่งหน้า (Pagination) มักใช้กับ Method GET (วิธีการส่งนี้ไม่เหมาะสำหรับส่งข้อมูลที่เป็นความลับ)

         ตัวอย่าง: จาก API ของผม ในระบบดึงรายการสินค้า (GET /api/products) หากในอนาคต Frontend ต้องการค้นหาเฉพาะคีย์บอร์ด จะต้องยิง URL มาเป็น GET /api/products?search=keyboard&maxPrice=3000 ซึ่งใน Controller ของผมจะสามารถดึงคำว่า keyboard และตัวเลข 3000 ไปใช้ค้นหาใน Database ได้ผ่านคำสั่ง req.query.search และ req.query.maxPrice ได้เลย *

---

**4. What are HTTP status codes? List every status code you used in your API and explain why you chose it for that situation.**

*Your answer: HTTP Status Codes คือ รหัสตัวเลข 3 หลักที่เป็นมาตรฐานสากล ซึ่งฝั่ง Server (Backend) ใช้ส่งกลับไปบอก Client (Frontend หรือเบราว์เซอร์) เพื่อแจ้งสถานะหรือผลลัพธ์ของการส่ง Request ว่าสำเร็จ ผิดพลาด หรือเกิดปัญหาอะไรขึ้น เปรียบเสมือนรหัสการสื่อสารสั้นๆ ให้ระบบทั้งสองฝั่งเข้าใจตรงกันและนำไปประมวลผลต่อได้อย่างถูกต้อง

จากไฟล์ Controller ในโปรเจกต์ของผม ผมได้เลือกใช้ Status Code 4 ตัวหลักๆ โดยจัดวางตามสถานการณ์และเหตุผลดังนี้:

     1. Code 200 OK (ใช้สำหรับดำเนินการสำเร็จ): การใช้งานใน API ผมใช้ในฟังก์ชันดึงข้อมูล (GET), อัปเดตข้อมูล (PUT) และลบข้อมูล (DELETE)

          ส่วนเหตุผลที่ผมใช้ คือใช้เพื่อเป็นการยืนยันกับ Frontend ว่า Request ที่ส่งมาได้รับการประมวลผลสำเร็จเรียบร้อยแล้ว และระบบ Backend กำลังส่งข้อมูลที่ต้องการกลับไปให้ (เช่น ส่งข้อมูลสินค้าที่เพิ่งแก้ไขเสร็จกลับไปให้ Frontend แสดงผล โดยก่อนที่เราจะส่งไปได้นั้น อาจจะมีโค้ดในการตรวจสอบความถูกต้องก่อนที่จะส่งกลับไป)

     2. Code 201 Created (ใช้สำหรับสร้างข้อมูลใหม่สำเร็จ): การใช้งานใน API ผมใช้เจาะจงเฉพาะในฟังก์ชันเพิ่มสินค้าใหม่ (POST / createProduct)
          ส่วนเหตุผลที่ผมใช้ คือในทางเทคนิคเราสามารถใช้ 200 แทนได้ แต่เพื่อความเป็นมืออาชีพและตามมาตรฐาน RESTful API ผมเลือกใช้ 201 เพื่อใช้สื่อความหมายให้ชัดเจนและแม่นยำ Code 201 นอกจากจะแจ้งว่า "สำเร็จ" แล้ว ยังแจ้งข้อความ "สร้างข้อมูล Record ใหม่สำเร็จ" ด้วย เมื่อข้อมูลบันทึกลงใน Database ของ MongoDB เรียบร้อย

     3. Code 404 Not Found (ใช้สำหรับไม่พบข้อมูลที่ต้องการ): การใช้งานใน API ผมใช้ในการดักจับเงื่อนไขในฟังก์ชันที่ต้องค้นหา อัปเดต หรือลบข้อมูลผ่าน ID (เช่น getProductById) หาก Database ค้นหาแล้วส่งค่ากลับมาว่าว่างเปล่า (null) หรือไม่มีสินค้านั้นอยู่จริง

          ส่วนเหตุผลที่ผมใช้ Code 404 ผมตั้งใจดักจับไว้เพื่อแจ้ง Frontend ว่ารหัส ID ที่ส่งมานั้นไม่มีในระบบ (อาจถูกลบไปแล้ว หรืออ้างอิงผิด) เพื่อให้ Frontend สามารถนำรหัส 404 นี้ไปจัดการเปลี่ยนหน้า UI เป็น "ไม่พบรายการสินค้า" ได้อย่างถูกต้อง แทนที่จะปล่อยให้ระบบแสดงผลหน้าจอขาวหรือค้างไปครับ

     4. Code 500 Internal Server Error (ใช้เมื่อเซิร์ฟเวอร์เกิดข้อผิดพลาด): การใช้งานใน API ผมใช้ใส่ครอบคลุมไว้ในบล็อก catch (error) ของทุกๆ ฟังก์ชันใน Controller

          ส่วนเหตุผลที่ผมใช้ ผมใช้เป็นด่านสุดท้าย (Safety Net) สำหรับดักจับ Error ที่เหนือการควบคุมของระบบ เช่น Database ล่ม, การเชื่อมต่อเครือข่ายมีปัญหา หรือโค้ดภายใน Backend รวน การส่ง 500 กลับไปเป็นการบอก Client แบบตรงไปตรงมาว่า "ปัญหาไม่ได้เกิดจากข้อมูลที่คุณส่งมาผิด แต่ระบบหลังบ้านของเรากำลังมีปัญหาเอง" ซึ่งการเขียนดักจับและส่ง Error ออกมาแบบนี้ จะช่วยป้องกันไม่ให้ Server ทั้งหมดหยุดทำงาน (Crash) เวลามีปัญหาครับ (สามารถดูตัวอย่างการดักจับได้ในไฟล์ backend/controllers/product.controllers.js) *  
---

**5. What is middleware? Describe what it does in your own words and give one example from your code.**

*Your answer:*

---

**6. Why does the order of middleware matter in Express? What could go wrong if it were in the wrong order?**

*Your answer:*

---

**7. Walk through what happens on the server, step by step, when a POST request is sent to `/products`.**

*Your answer:*

---

**8. What is CRUD? Map each operation to the HTTP method and route you used in your API.**

*Your answer:*

---

**9. How does your API respond when something goes wrong — for example, when a product with a given ID does not exist?**

*Your answer:*

---

**10. What was the hardest part of building this API and what did you do to get past it?**

*Your answer:*
