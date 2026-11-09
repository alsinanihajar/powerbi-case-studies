# حل Case Study: مقاهي حبّة ☕ (للمدرّبة)

## المهمة 1: الخطة

| العمود | السؤال | النوع | وين يروح |
|---|---|---|---|
| OrderID | رقم الطلب | Dimension (Degenerate) | **FactSales** |
| OrderDate | متى؟ | Dimension | **FactSales** (يرتبط بجدول التاريخ لاحقاً) |
| CustomerID | مين؟ | مفتاح | **DimCustomer** (Primary Key) + **FactSales** (Foreign Key) |
| CustomerName | مين؟ | Dimension | **DimCustomer** |
| CustomerAge | مين؟ | Dimension | **DimCustomer** |
| CustomerCity | مين؟ | Dimension | **DimCustomer** |
| ProductID | وش؟ | مفتاح | **DimProduct** (Primary Key) + **FactSales** (Foreign Key) |
| ProductName | وش؟ | Dimension | **DimProduct** |
| Category | وش؟ | Dimension | **DimProduct** |
| Size | وش؟ | Dimension | **DimProduct** |
| BranchName | وين؟ | Dimension | **DimBranch** |
| BranchCity | وين؟ | Dimension | **DimBranch** |
| PaymentMethod | كيف؟ | Dimension | **FactSales** (أو DimPayment كتحدي) |
| Qty | كم؟ | **Fact** | **FactSales** |
| UnitPrice | كم؟ | **Fact** | **FactSales** |
| LineTotal | كم؟ | **Fact** | **FactSales** |

---

## المهمة 2: الجداول المتوقعة

### DimCustomer: 8 صفوف
**المفتاح: CustomerID** (جاهز من النظام، ما يحتاج Index)

| CustomerID | CustomerName | CustomerAge | CustomerCity |
|---|---|---|---|
| C001 | نورة سعد | 22 | الرياض |
| C002 | نورة سعد | 58 | الرياض |
| C003 | ريم خالد | 51 | الرياض |
| C004 | لمى عبدالله | 19 | جدة |
| C005 | سارة محمد | 29 | جدة |
| C006 | هيا فهد | 36 | الرياض |
| C007 | دانة علي | 25 | جدة |
| C008 | جود ناصر | 44 | الرياض |

> ⚠️ **الفخ الأول:** فيه عميلتين اسمهم **نورة سعد** من **الرياض**. لو سوت الطالبة Remove Duplicates على **CustomerName + CustomerCity**، بيطلع **7** عميلات بدل 8، وبتندمج نورتين في وحدة.
>
> **الصحيح:** Remove Duplicates على **CustomerID**. وهذا يثبت النقطة اللي ناقشناها: **الأسماء تتشابه، والـ ID هو اللي يميّز**.

### DimProduct: 9 صفوف
**المفتاح: ProductID** (جاهز من النظام)

> ⚠️ **الفخ الثاني:** **لاتيه** موجود بحجمين (صغير P01 وكبير P02). لو سوت الطالبة Remove Duplicates على **ProductName** بس، بيطلع **8** منتجات، ويضيع الفرق بين الحجمين.
>
> **الصحيح:** على **ProductID**، أو على ProductName + Size.

### DimBranch: 3 صفوف
**المفتاح: ما فيه، لازم تصنعه** بـ **Index Column** وتسميه **BranchKey**.

| BranchKey | BranchName | BranchCity |
|---|---|---|
| 1 | فرع ... | ... |
| 2 | فرع ... | ... |
| 3 | فرع ... | ... |

(الترتيب يعتمد على ترتيب الصفوف عند الطالبة: فرع العليا والنخيل في الرياض، وفرع الروضة في جدة.)

> ⚠️ **الفخ الثالث:** الفرع ما له ID جاهز، فلازم تصنعه بـ Index، وبعدها **Merge** في FactSales على **BranchName** لجلب **BranchKey**. يعني نفس خطوات مختبر الفاتورة.

### FactSales: 51 صف
**الأعمدة:** OrderID، وOrderDate، وCustomerID، وProductID، وBranchKey، وPaymentMethod، وQty، وUnitPrice، وLineTotal.

> ⚠️ **الفخ الرابع:** لو حذفت **BranchName** قبل ما تسوي Merge لجلب BranchKey، ما بتقدر تجيب المفتاح. الحذف يكون **بعد** كل الـ Merges.

---

## المهمة 3: الموديل

```
DimCustomer (1) ──── (*) FactSales (*) ──── (1) DimProduct
                             │
                            (*)
                             │
                            (1)
                         DimBranch
```

- 3 علاقات، كلها **One-to-Many**، و**Single**.
- **Orders**: Enable load ملغي.
- المفاتيح مخفية: CustomerID، وProductID، وBranchKey في الجداول كلها.

> ⚠️ لو Power BI خمّن أي علاقة **1:1**، لازم تعدلها الطالبة بيدها.

---

## المهمة 4: الأجوبة الصحيحة

| # | السؤال | الطريقة | الجواب |
|---|---|---|---|
| 1 | عدد العميلات | Card: Count على CustomerName من **DimCustomer** | **8** |
| 2 | عدد المنتجات | Card: Count على ProductName من **DimProduct** | **9** |
| 3 | عدد الطلبات | Card: **Count (Distinct)** على OrderID من **FactSales** | **24** (وعدد الأسطر 51) |
| 4 | المبيعات حسب الفرع | BranchName من DimBranch + Sum LineTotal | الروضة **504**، العليا **326**، النخيل **275**. الإجمالي **1,105** |
| 5 | المبيعات حسب الفئة | Category من DimProduct + Sum LineTotal | قهوة ساخنة **430**، قهوة باردة **349**، حلويات **326** |
| 6 | المبيعات حسب مدينة العميلة | CustomerCity من DimCustomer + Sum LineTotal | الرياض **601**، جدة **504** |
| 7 | المبيعات لكل عميلة | CustomerName (+ CustomerID) من DimCustomer + Sum LineTotal | الجدول تحت |
| 8 | متوسط الأعمار | Card: Average على CustomerAge من **DimCustomer** | **35.5** |

**المبيعات لكل عميلة:**

| CustomerID | CustomerName | المبيعات |
|---|---|---|
| C001 | نورة سعد | 184 |
| C002 | نورة سعد | 69 |
| C003 | ريم خالد | 87 |
| C004 | لمى عبدالله | 209 |
| C005 | سارة محمد | 122 |
| C006 | هيا فهد | 115 |
| C007 | دانة علي | 173 |
| C008 | جود ناصر | 146 |
| | **Total** | **1,105** |

> ⚠️ لو حطت الطالبة **CustomerName بس** في الجدول، النورتين بيطلعون **صف واحد = 253**. هذا مو غلط في الموديل، بس الـ visual جمّع حسب الاسم. والحل: تضيف **CustomerID** في الجدول.

---

## المهمة 5: المقارنة

| السؤال | من الجدول المسطح (Orders) | من الموديل | ليش الفرق؟ |
|---|---|---|---|
| متوسط الأعمار | **33.4** ❌ | **35.5** ✅ | في الجدول المسطح كل عميلة متكررة بعدد أسطر مشترياتها. ولمى (19 سنة) عندها 8 أسطر، فعمرها انحسب 8 مرات وسحب المتوسط لتحت |
| عدد العميلات (Distinct على الاسم) | **7** ❌ | **8** ✅ | النورتين لهم نفس الاسم، فانحسبوا وحدة |

---

## المهمة 6: قراءة العلاقات

- **العميلة** الوحدة لها **مشتريات** كثيرة، وكل سطر مشتريات يرجع لـ **عميلة** وحدة.
- **المنتج** الواحد له **مبيعات** كثيرة، وكل سطر مبيعات فيه **منتج** واحد.
- **الفرع** الواحد له **مبيعات** كثيرة، وكل سطر مبيعات صار في **فرع** واحد.

---

## أسئلة النقاش

1. **CustomerCity وBranchCity:** ما يروحون لنفس الجدول. CustomerCity **يوصف العميلة** (وين ساكنة)، فيروح DimCustomer. وBranchCity **يوصف الفرع** (وين موقعه)، فيروح DimBranch. **نفس نوع المعلومة، بس كل وحدة توصف شي مختلف.**

2. **PaymentMethod:** Dimension، لأنه يجاوب **كيف دفعت؟** ونقسّم عليه، وما نجمعه. مكانه في FactSales لأنه يخص **الطلب** نفسه وما له أوصاف ثانية. أو جدول صغير **DimPayment** (3 صفوف) كتحدي.

3. **OrderID:** ما نسوي له جدول، لأنه ما عنده أوصاف ثانية. فيبقى في FactSales كـ **Degenerate Dimension**، ونستخدمه في **عدّ الطلبات**.

4. **DimCustomer ما فيه كل العميلات.** فيه بس العميلات اللي **طلبوا**، لأننا بنيناه من جدول الطلبات. أي عميلة سجّلت في التطبيق وما طلبت (مثل خالد في الدرس) **ناقصة**. والحل في الواقع: نبني DimCustomer من **جدول العملاء الأصلي** في النظام، مو من جدول الطلبات.

5. **رقم الجوال:**
   - **في الموديل:** مكان واحد، DimCustomer.
   - **في الجدول المسطح:** يتكرر في كل سطر طلب للعميلة، ولو فيه جداول مدموجة ثانية (مثل المرتجعات) لازم يتعدل فيها كلها.
