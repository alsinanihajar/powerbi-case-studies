# Case Study: BeanHouse Coffee ☕

**BeanHouse** سلسلة مقاهي، وعندنا ملف طالع من نظام الكاشير حقها: **`CaseStudy_CoffeeShop.xlsx`**، شيت **`Orders`**.

كل صف في الجدول = **منتج واحد في طلب واحد**.

هذي أول 6 صفوف من الجدول:

| OrderID | OrderDate | CustomerID | CustomerName | CustomerAge | CustomerCity | ProductID | ProductName | Category | Size | BranchName | BranchCity | PaymentMethod | Qty | UnitPrice | LineTotal |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| ORD-1021 | 2026-02-03 | C006 | هيا فهد | 36 | الرياض | P01 | لاتيه | قهوة ساخنة | صغير | فرع العليا | الرياض | Apple Pay | 2 | 14 | 28 |
| ORD-1021 | 2026-02-03 | C006 | هيا فهد | 36 | الرياض | P09 | كرواسون | حلويات | - | فرع العليا | الرياض | Apple Pay | 1 | 10 | 10 |
| ORD-1011 | 2026-02-05 | C004 | لمى عبدالله | 19 | جدة | P02 | لاتيه | قهوة ساخنة | كبير | فرع الروضة | جدة | مدى | 2 | 18 | 36 |
| ORD-1011 | 2026-02-05 | C004 | لمى عبدالله | 19 | جدة | P04 | سبانش لاتيه | قهوة باردة | وسط | فرع الروضة | جدة | مدى | 1 | 19 | 19 |
| ORD-1018 | 2026-02-05 | C002 | نورة سعد | 58 | الرياض | P01 | لاتيه | قهوة ساخنة | صغير | فرع العليا | الرياض | مدى | 2 | 14 | 28 |
| ORD-1018 | 2026-02-05 | C002 | نورة سعد | 58 | الرياض | P09 | كرواسون | حلويات | - | فرع العليا | الرياض | مدى | 1 | 10 | 10 |

---

## المطلوب

### 1. صنّفوا الأعمدة

لكل عمود من الـ 16 عمود: هل هو **Fact** أو **Dimension**؟

| العمود | Fact / Dimension |
|---|---|
| OrderID | |
| OrderDate | |
| CustomerID | |
| CustomerName | |
| CustomerAge | |
| CustomerCity | |
| ProductID | |
| ProductName | |
| Category | |
| Size | |
| BranchName | |
| BranchCity | |
| PaymentMethod | |
| Qty | |
| UnitPrice | |
| LineTotal | |

### 2. افصلوا الجدول

ابدأوا افصلوا جدول **Orders** في Power Query إلى **4 جداول** بهالأسماء:

| اسم الجدول | وش فيه |
|---|---|
| **DimCustomer** | معلومات العميلة |
| **DimProduct** | معلومات المنتج |
| **DimBranch** | معلومات الفرع |
| **FactSales** | المفاتيح والأرقام |

وحددوا لكل Dimension **وش المفتاح** حقه.

### 3. اربطوا الجداول

في **Model view** اربطوا الـ Dimensions بالـ **FactSales**.

---

## التسليم

1. جدول التصنيف (المطلوب 1).
2. صورة من **Model view** فيها الجداول الأربعة والعلاقات.
