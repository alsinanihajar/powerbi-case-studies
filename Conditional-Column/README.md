<div dir="rtl">

# العمود الشرطي (Conditional Column) في Power Query

**إعداد: هاجر السناني**
© جميع الحقوق محفوظة

> هذا الملف من جزأين: **شرح** بأمثلة محلولة على بيانات شركة طيران، ثم **تمرين** على بيانات مختلفة تمامًا لنادٍ رياضي.
> البيانات وهمية أُعدّت لأغراض التدريب.

---

# الجزء الأول: الشرح

## ما هو العمود الشرطي؟

عمود جديد نصنعه من عمود موجود، بقاعدة بسيطة:

> **إذا** تحقق شرط ← اكتب قيمة،
> **وإلا إذا** تحقق شرط آخر ← اكتب قيمة أخرى،
> **وإلا** ← اكتب قيمة أخيرة.

**أين نجده؟** `Add Column ← Conditional Column`

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

## ثلاث قواعد ذهبية

1. **الترتيب مهم:** يقرأ Power Query الشروط من الأعلى إلى الأسفل، ويتوقف عند **أول شرط يتحقق**.
2. **ابدأ بالقيمة المفقودة:** إن كان العمود فيه `null`، فاجعل شرط `null` أول سطر.
3. **لا تترك Else فارغًا دون قصد:** إن لم يتحقق أي شرط ولم تكتب شيئًا، كانت النتيجة `null`.

> **حدود الأداة:** لا يمكن الجمع بين شرطين في سطر واحد (AND / OR). لهذا نستخدم **Custom Column**.

---

## أمثلة محلولة: شركة SkyLine Airways

**الملف:** [`data/skyline_flights.csv`](data/skyline_flights.csv)، وفيه 20 رحلة.

| العمود | الوصف | مثال |
|---|---|---|
| `Flight_No` | رقم الرحلة: `SL` داخلية، `SX` دولية | `SX361` |
| `Flight_Date` | تاريخ الرحلة | `2024-09-01` |
| `Origin` | مدينة الإقلاع | `London` |
| `Destination` | مدينة الوصول | `Madrid` |
| `Seat_Class` | درجة المقعد: `Y` سياحية، `J` أعمال، `F` أولى | `Y` |
| `Delay_Minutes` | دقائق التأخير | `75` |
| `Passenger_Rating` | تقييم الراكب من 1 إلى 5، وفارغ إذا لم يقيّم | `5` |

### المثال ١: رمز إلى اسم (equals)

عمود `Class_Name` من `Seat_Class`:

| Operator | Value | Output |
|---|---|---|
| equals | `Y` | `Economy` |
| equals | `J` | `Business` |
| equals | `F` | `First` |

**Else:** `Unknown`

**النتيجة:** Economy = 12، Business = 5، First = 3

### المثال ٢: بداية النص (begins with)

عمود `Flight_Type` من `Flight_No`:

| Operator | Value | Output |
|---|---|---|
| begins with | `SL` | `Domestic` |

**Else:** `International`

**النتيجة:** International = 11، Domestic = 9

> لاحظ: شرط واحد يكفي، و **Else** يتكفل بالباقي.

### المثال ٣: فئات الأرقام، وترتيب الشروط

عمود `Delay_Status` من `Delay_Minutes`:

| Operator | Value | Output |
|---|---|---|
| is less than or equal to | `15` | `On time` |
| is less than or equal to | `60` | `Delayed` |

**Else:** `Severely delayed`

**النتيجة:** On time = 13، Delayed = 5، Severely delayed = 2

> ⚠️ **لماذا الترتيب مهم؟** لو بدأنا بـ `is less than or equal to 60 ← Delayed`، لصارت الرحلات التي تأخرت 5 دقائق «متأخرة»، لأن 5 أقل من 60، فيتوقف Power Query عند أول شرط.

### المثال ٤: القيم المفقودة (null)

عمود `Rating_Group` من `Passenger_Rating`:

| Operator | Value | Output |
|---|---|---|
| equals | `null` | `No rating` |
| is greater than or equal to | `4` | `Happy` |

**Else:** `Not happy`

**النتيجة:** Happy = 16، Not happy = 2، No rating = 2

> ⚠️ لو حذفنا سطر `null`، لصار الركاب الذين لم يقيّموا «غير راضين»، وهذا حكم ظالم.

### خلف الكواليس: لغة M

هذا المثال ٣ كما يظهر في شريط الصيغة:

```m
= Table.AddColumn(#"Changed Type", "Delay_Status", each
    if [Delay_Minutes] <= 15 then "On time"
    else if [Delay_Minutes] <= 60 then "Delayed"
    else "Severely delayed")
```

---

# الجزء الثاني: التمرين

## نادي FitZone الرياضي

يريد مدير النادي فهم أعضائه: من هم؟ وما خططهم؟ ومن منهم مهدد بإلغاء اشتراكه؟ البيانات موجودة، لكن أغلبها رموز وأرقام لا يفهمها المدير مباشرة.

**الملف:** [`data/fitzone_members.csv`](data/fitzone_members.csv)، وفيه 40 عضوًا.

**تنزيل الملف:** افتح رابط الملف، ثم اضغط زر **Download raw file**.

| العمود | الوصف | مثال |
|---|---|---|
| `Member_ID` | رقم العضو | `FZ-2001` |
| `First_Name` | الاسم الأول | `Chloe` |
| `Age` | العمر | `47` |
| `Plan_Code` | رمز الخطة: `B` أساسية، `S` قياسية، `P` مميزة | `S` |
| `Email` | البريد الإلكتروني | `chloe18@techcorp.com` |
| `Promo_Code` | رمز العرض المستخدم عند التسجيل، وفارغ إذا لم يُستخدم عرض | `STU-2024` |
| `Monthly_Fee` | الرسوم الشهرية بالدولار | `45` |
| `Visits_Last_Month` | عدد الزيارات في الشهر الماضي، وفارغ إذا لم تُسجَّل | `18` |

**قبل البدء:** `Get data ← Text/CSV ← fitzone_members.csv ← Transform Data`

## المطلوب

أنشئ ستة أعمدة شرطية على البيانات نفسها:

- [ ] **١. `Plan_Name`** من `Plan_Code`: حوّل الرمز إلى اسم الخطة: `Basic` و `Standard` و `Premium`.
- [ ] **٢. `Age_Group`** من `Age`:
  - أقل من 18 ← `Teen`
  - أقل من 30 ← `Young Adult`
  - أقل من 50 ← `Adult`
  - غير ذلك ← `Senior`
- [ ] **٣. `Email_Type`** من `Email`: البريد الذي ينتهي بـ `@techcorp.com` ← `Corporate`، وغير ذلك ← `Personal`.
- [ ] **٤. `Promo_Type`** من `Promo_Code`:
  - الخانة الفارغة ← `No promo`
  - الرمز الذي يحتوي على `STU` ← `Student`
  - غير ذلك ← `Other promo`
- [ ] **٥. `Activity_Level`** من `Visits_Last_Month`:
  - الخانة الفارغة ← `No data`
  - 12 زيارة أو أكثر ← `Very active`
  - 4 زيارات أو أكثر ← `Regular`
  - غير ذلك ← `At risk`
- [ ] **٦. `Final_Fee`** من العمود الذي أنشأته في التمرين ٤: إذا كان `Promo_Type` يساوي `Student` ← `20`، وغير ذلك ← قيمة `Monthly_Fee` نفسها.

---

<details>
<summary><b>تلميحات (افتحها عند الحاجة فقط)</b></summary>

- **التمرين ٢:** انتبه لترتيب الشروط. أيهما يجب أن يأتي أولًا: أقل من 18 أم أقل من 50؟
- **التمرين ٣:** ابحث في قائمة Operator عن مقارنة تفحص **نهاية** النص.
- **التمرين ٤:** الخانة الفارغة في ملف CSV نص فارغ، لا `null`. حوّلها أولًا إلى `null` كما تعلمنا: Trim، ثم Replace Values. ثم اجعل شرط `null` أول سطر؛ فلو جاء شرط `contains` قبله، ظهر **Error** في الصفوف الفارغة.
- **التمرين ٥:** أين تضع شرط الخانة الفارغة؟
- **التمرين ٦:** في خانة **Else** افتح قائمة (ABC 123) واختر **Select a column**.

</details>

<details>
<summary><b>تحقّق من نتيجتك</b></summary>

| العمود | النتيجة المتوقعة |
|---|---|
| `Plan_Name` | Basic = 21، Standard = 10، Premium = 9 |
| `Age_Group` | Young Adult = 16، Teen = 9، Adult = 8، Senior = 7 |
| `Email_Type` | Personal = 28، Corporate = 12 |
| `Promo_Type` | Student = 16، No promo = 14، Other promo = 10 |
| `Activity_Level` | At risk = 16، Regular = 12، Very active = 11، No data = 1 |
| `Final_Fee` | مجموعها 1,345 دولارًا (مجموع `Monthly_Fee` الأصلي 1,710) |

</details>

---

## أسئلة للمراجعة

1. في التمرين ٢، ماذا يحدث لو بدأت بشرط «أقل من 50»؟
2. ما الفرق بين **contains** و **begins with**؟ ومتى تستخدم كل واحد؟
3. لماذا لا يكفي **Conditional Column** إذا أردنا: «طالب **و** عمره أقل من 25»؟

---

<p align="center">إعداد: هاجر السناني · © جميع الحقوق محفوظة</p>

</div>
