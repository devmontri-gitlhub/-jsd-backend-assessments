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

*Your answer:*

---

**4. What are HTTP status codes? List every status code you used in your API and explain why you chose it for that situation.**

*Your answer:*

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
