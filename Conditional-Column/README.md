<div dir="rtl">

# مرجع: العمود الشرطي (Conditional Column) في Power Query

**إعداد: هاجر السناني**
© جميع الحقوق محفوظة

> مرجع مختصر مع تمرين تطبيقي على بيانات متجر إلكتروني وهمي اسمه **Urban Cart**.
> البيانات وهمية أُعدّت لأغراض التدريب.

---

## ما هو العمود الشرطي؟

عمود جديد نصنعه من عمود موجود، بقاعدة بسيطة:

> **إذا** تحقق شرط ← اكتب قيمة،
> **وإلا إذا** تحقق شرط آخر ← اكتب قيمة أخرى،
> **وإلا** ← اكتب قيمة أخيرة.

**متى نحتاج إليه؟**
- تحويل رموز إلى أسماء مفهومة: `US` ← `United States`.
- تصنيف الأرقام في فئات: مبلغ ٢٥ ← `Small`.
- تصنيف النصوص حسب بدايتها أو محتواها: `EL-125` ← `Electronics`.
- إعطاء القيم المفقودة اسمًا واضحًا: `null` ← `Not rated`.

**أين نجده؟** `Add Column ← Conditional Column`

---

## أجزاء النافذة

| الجزء | معناه |
|---|---|
| **New column name** | اسم العمود الجديد |
| **Column Name** | العمود الذي نفحصه |
| **Operator** | نوع المقارنة |
| **Value** | القيمة التي نقارن بها |
| **Output** | ما يُكتب إذا تحقق الشرط |
| **Add Clause** | يضيف سطر «وإلا إذا» جديدًا |
| **Else** | ما يُكتب إذا لم يتحقق أي شرط |

> بجانب **Value** و **Output** قائمة صغيرة (ABC 123) تتيح اختيار **عمود** بدل قيمة ثابتة.

### أنواع المقارنة (Operator)

| للنصوص | للأرقام |
|---|---|
| equals | equals |
| does not equal | does not equal |
| begins with | is greater than |
| does not begin with | is greater than or equal to |
| ends with | is less than |
| does not end with | is less than or equal to |
| contains | |
| does not contain | |

---

## ثلاث قواعد ذهبية

1. **الترتيب مهم:** يقرأ Power Query الشروط من الأعلى إلى الأسفل، ويتوقف عند **أول شرط يتحقق**.
2. **ابدأ بالقيمة المفقودة:** إن كان العمود فيه `null`، فاجعل شرط `null` أول سطر، وإلا وقعت القيم المفقودة في شرط آخر خطأً.
3. **لا تترك Else فارغًا دون قصد:** إن لم يتحقق أي شرط ولم تكتب شيئًا في Else، كانت النتيجة `null`.

> **حدود الأداة:** لا يمكن الجمع بين شرطين في سطر واحد (AND / OR)، مثل: «المبلغ أكبر من 100 **و** الدولة Germany». لهذا نستخدم **Custom Column** ونكتب الصيغة بأنفسنا.

---

## التمرين التطبيقي

### البيانات

| الملف | الوصف |
|---|---|
| [`data/urban_cart_orders.csv`](data/urban_cart_orders.csv) | 30 طلبًا من متجر Urban Cart في مارس 2024 |

**تنزيل الملف:** افتح رابط الملف، ثم اضغط زر **Download raw file**.

| العمود | الوصف | مثال |
|---|---|---|
| `OrderID` | رقم الطلب | `UC-10001` |
| `Order_Date` | تاريخ الطلب | `2024-03-01` |
| `Country_Code` | رمز الدولة | `GB` |
| `SKU` | رمز المنتج، وأول حرفين منه يدلان على الفئة | `EL-125` |
| `Product_Name` | اسم المنتج | `Wireless Earbuds` |
| `Quantity` | الكمية | `1` |
| `Order_Total` | قيمة الطلب بالدولار | `59` |
| `Customer_Rating` | تقييم العميل من 1 إلى 5، وفارغ إذا لم يقيّم | `5` |

**قبل البدء:** `Get data ← Text/CSV ← urban_cart_orders.csv ← Transform Data`

---

### التمرين ١: تحويل رمز إلى اسم (equals)

أنشئ عمود `Country` من `Country_Code`:

| Operator | Value | Output |
|---|---|---|
| equals | `US` | `United States` |
| equals | `GB` | `United Kingdom` |
| equals | `DE` | `Germany` |
| equals | `FR` | `France` |
| equals | `CA` | `Canada` |

**Else:** `Other`

---

### التمرين ٢: التصنيف من بداية النص (begins with)

أنشئ عمود `Category` من `SKU`:

| Operator | Value | Output |
|---|---|---|
| begins with | `EL` | `Electronics` |
| begins with | `HM` | `Home` |
| begins with | `SP` | `Sports` |

**Else:** `Other`

---

### التمرين ٣: فئات الأرقام، وترتيب الشروط

أنشئ عمود `Order_Size` من `Order_Total`:

| Operator | Value | Output |
|---|---|---|
| is less than | `50` | `Small` |
| is less than or equal to | `150` | `Medium` |

**Else:** `Large`

> 🧪 **جرّب الخطأ عمدًا:** أنشئ عمودًا آخر بترتيب معكوس:
> `is greater than 50 ← Medium` ثم `is greater than 150 ← Large`.
> لماذا لم يظهر أي طلب `Large`؟

---

### التمرين ٤: القيم المفقودة (null)

أنشئ عمود `Rating_Group` من `Customer_Rating`:

| Operator | Value | Output |
|---|---|---|
| equals | `null` | `Not rated` |
| is greater than or equal to | `4` | `Satisfied` |
| equals | `3` | `Neutral` |

**Else:** `Unsatisfied`

> 🧪 **جرّب الخطأ عمدًا:** احذف سطر `null`. أين ذهب العملاء الذين لم يقيّموا؟ ولماذا يُعد ذلك ظلمًا للمتجر؟

---

<details>
<summary><b>تحقّق من نتيجتك</b></summary>

| العمود | النتيجة المتوقعة |
|---|---|
| `Country` | France = 8، United Kingdom = 6، United States = 6، Germany = 6، Canada = 4 |
| `Category` | Electronics = 12، Home = 10، Sports = 8 |
| `Order_Size` | Small = 13، Medium = 10، Large = 7 |
| `Order_Size` بالترتيب المعكوس | لا يوجد Large، والطلبات الـ 7 الكبيرة صُنّفت Medium |
| `Rating_Group` | Satisfied = 15، Unsatisfied = 6، Not rated = 5، Neutral = 4 |

> **طريقة التحقق السريعة:** حدد العمود، ثم افتح سهم الفلتر لترى القيم، أو استخدم Column distribution.

</details>

---

## خلف الكواليس: لغة M

كل عمود شرطي يُكتب تلقائيًا بلغة M. هذا مثال التمرين ٣ كما يظهر في شريط الصيغة:

```m
= Table.AddColumn(#"Changed Type", "Order_Size", each
    if [Order_Total] < 50 then "Small"
    else if [Order_Total] <= 150 then "Medium"
    else "Large")
```

لاحظ أن `if` و `else if` و `else` هي نفسها سطور النافذة.

---

## أسئلة للمراجعة

1. ما الفرق بين **Conditional Column** و **Replace Values** في تحويل الرموز إلى أسماء؟
2. لماذا نضع شرط `null` في أول سطر؟
3. متى نحتاج إلى **Custom Column** بدل **Conditional Column**؟

---

<p align="center">إعداد: هاجر السناني · © جميع الحقوق محفوظة</p>

</div>
