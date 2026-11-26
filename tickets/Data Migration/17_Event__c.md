# Object: Event__c

## Salesforce Object Details

| Property | Value |
|----------|--------|
| Object Label | Event |
| Plural Label | Events |
| API Name | Event__c |
| Hebrew Label | אירוע |
| Owner | Etti (YV20-247) |
| Status | Per task (D-036) |
| Task | YV20-247 |

The event: the offer entity of the events world. It represents an activity that Yad Vashem publishes and the public registers to: show, spectacle, open tour, study day, conference, training, seminar and course (Doc 3 3.2.1, Doc 1 4.3). It holds the type, dates, capacity, status and branding, and is linked to a Product (which sets the registration price) or to a Bundle (C2 in `knowledge/dd-corrections.md`) and to the registration form. Its sessions and resource scheduling live in GoMeddo (`B25__Reservation__c`), not here.

Legacy entity: `new_event_reservations` ("ארועים מערכת אחודה", code 10277, 117 fields). Doc 1 4.3: "in the current system this entity was called אירועים מערכת אחודה". This task builds the object and the fields the ticket needs (name, type, product, status) plus capacity and payment status from Doc 3 3.2.1. The full plan, the proposed later fields and the screens are in [YV20-247_Plan.md](../../plan/YV20-247_Plan.md).

No object file or object existed before this task (`force-app` has no `Event__c`). Drafted without a live org check: `yv-partial` was not authorized on the drafting machine, so the clash check in `CONVENTIONS.md` is still to do.

## Field Mapping

| Salesforce Label | Salesforce API Name | Translation | Field Type | Picklist Values | שם השדה במערכת הישנה | Old API Name | Unique | Section | Questions | Task | Status |
|-----------------|--------------------|-------------|------------|------------------|----------------------|--------------|--------|---------|-----------|------|--------|
| Event Name | Name | שם | Text(80) | | שם | new_name | | General Info | [Implementer] Standard Name, capped at 80 characters. The legacy length is not in the fields file, so this is unconfirmed against the data. | YV20-247 | To build |
| Event Type | Event_Type__c | סוג אירוע | Picklist | Professional Development;Study Day;Seminar;Online Orders Tour;Training;Other;Conference;Event;Show;Spectacle;Open Tour;Course | סוג אירוע | new_event_type | | General Info | [Lior] Doc 3 3.2.1 also names "activity type" (סוג הפעילות, required, gates the products): same field? [Lior] Values are the legacy list plus the four DD types missing from it (Show, Spectacle, Open Tour, Course); the legacy test value "FOR TEST!" is left out. Is Online Orders Tour (legacy סיור מערכת הזמנות מקוונת) the same as Open Tour (legacy סיור פתוח)? [Lior] Doc 3 chapters 5.3 to 5.6 also name record types (conference, seminar, course, training): record types, this picklist, or both? Required by the DD; enforced by a validation rule and the page, not at database level, so the legacy load can run. | YV20-247 | To build |
| Registration Product | Product__c | מוצר ההרשמה | Lookup(Product2) | | | | | General Info | [Implementer] No legacy field. From Doc 3 3.2.1 and C2: the event links to a Product, and the registration price is derived from it. The DD names it "מוצר ההרשמה" (Doc 3 5.5). The Bundle lookup for events sold as a package is a later task. | YV20-247 | To build |
| Event Status | Event_Status__c | סטטוס אירוע | Picklist | New;Handling Completed;Cancelled;Pending Scheduling;Pending Orders Center Approval;Quote | סטטוס אירוע | new_event_reservation_status | | General Info | [Lior] The legacy values read like order statuses. The image of 26.11.2026 lists 6 values; Draft (טיוטה, DD A15-01) is not built. Add it if still needed. Which statuses does the event really need? | YV20-247 | Built |
| Payment Status | Payment_Status__c | סטטוס תשלום | Picklist | None;Paid;Not Paid;Paid and Fully Credited;Paid and Partially Credited;Paid (Internal Settlement);Not Paid (Internal Settlement);Place Reserved - No Payment Needed;Awaiting Payment;No Payment Needed;Payment Commitment;Voucher Received;Payment Failed;Account Issued;Partially Paid;Account Cancelled;Account Partially Issued;Transferred to Finance;To Be Charged in Package | סטטוס תשלום | new_payment_status | | General Info | [Anna] All 19 legacy values copied. Several (Account Issued, Account Cancelled) overlap the financial account match status in Doc 3 3.3.1. Keep all? | YV20-247 | Built |
| Max Participants Per Event | Max_Participants_Per_Event__c | מקסימם משתתפים לאירוע | Number(18,0) | | מקסימם משתתפים לאירוע | new_max_participants_per_event | | Capacity | [Implementer] The legacy label has a typo ("מקסימם"); kept exactly as in the source. Doc 3 3.2.1: the capacity is required. [Lior] Required by a validation rule plus the page instead of at database level, since legacy rows may be blank. Confirm. [Lior] The default capacity per event type: custom metadata or a type object? (Later automation, not this task.) | YV20-247 | Built |
| Max Participants Per Registration | Max_Participants_Per_Registration__c | מקסימום משתתפים לרישום בודד | Number(18,0) | | מקסימום משתתפים לרישום בודד | new_max_participants_per_registration | | Capacity | [Implementer] Source V14-11. | YV20-247 | Built |
| Total Participants | Total_Participants__c | סה"כ נרשמים | Number(18,0) | | סה"כ נרשמים | new_total_participants | | Capacity | [Implementer] Plain number for now; occupancy is calculated later (plan Q11). | YV20-247 | Built |
| Additional Participants | Additional_Participants__c | מספר מבקרים נוספים | Number(18,0) | | מספר מבקרים נוספים | new_num_additional_participants | | Capacity | [Implementer] DD 5.5. | YV20-247 | Built |
| Divisions For Registration | Divisions_For_Registration__c | כמות חטיבות לרישום | Number(18,0) | | כמות חטיבות לרישום | new_divisions_for_registration | | Capacity | [Implementer] Legacy int; the DD meaning is to be confirmed. | YV20-247 | Built |
| Duration Hours | Duration_Hours__c | אורך ההשתלמות (בשעות) | Number(18,0) | | אורך ההשתלמות (בשעות) | new_duration_hours | | General Info | | YV20-247 | Built |
| Coupon Discount | Coupon_Discount__c | הנחת קופון | Number(18,2) | | הנחת קופון | new_coupon_discount | | Payment | [Implementer] Number(18,2), the only legacy int with 2 decimals. Amount or percent is undecided (coupon task). | YV20-247 | Built |
| Exchange Rate | Exchange_Rate__c | שער המרה | Number(16,2) | | Exchange Rate | exchangerate | | Payment | [Implementer] Legacy Dynamics system field (decimal). Two decimals as chosen; legacy may hold more, so check the data for rounding. The plan had held it back as a system field. | YV20-247 | Built |
| Target Audience | Target_Audience__c | קהל יעד | Picklist | 1 to 48 | קהל יעד | new_age_group | | General Info | [Implementer] Numbered: many values have no simple English translation. The existing global value set Age_Group holds the same 48 Hebrew values but is not used. | YV20-247 | Built |
| Cancellation Reason | Cancellation_Reason__c | סיבה לביטול האירוע | Picklist | 1 to 20 | סיבה לביטול האירוע | new_cancellation_reason | | General Info | [Implementer] Numbered: several values have no simple English translation. The existing global value set Cancel_Reason holds the same 20 Hebrew values but is not used. | YV20-247 | Built |
| Day Of Week | Day_Of_Week__c | יום בשבוע | Picklist | Sunday;Monday;Tuesday;Wednesday;Thursday;Friday;Saturday | יום בשבוע | new_day_of_week | | General Info | | YV20-247 | Built |
| Frequency | Frequency__c | תדירות | Picklist | Daily;Weekly;Every Two Weeks;Monthly;Manual | תדירות | new_frequency | | General Info | | YV20-247 | Built |
| Discount Type | Discount_Type__c | סוג הנחה | Picklist | 1 to 5 | סוג הנחה | new_discount_type | | Payment | [Implementer] Numbered: "משואה לתקומה" has no simple English translation. Hebrew: 1 ללא, 2 סכום, 3 אחוז, 4 משרד החינוך, 5 משואה לתקומה. | YV20-247 | Built |
| Geographic District | Geographic_District__c | מחוז גאוגרפי | Picklist | 1 to 18 | מחוז גאוגרפי | new_district | | General Info | [Implementer] Numbered: the list mixes districts and school sectors. Plan row Region__c (DD 5.6) stays open. | YV20-247 | Built |
| Training Type | Training_Type__c | סוג השתלמות | Picklist | Haredi;Regular;Colleges | סוג השתלמות | new_training_type | | General Info | | YV20-247 | Built |
| Gefen Type | Gefen_Type__c | סוג הגפן | Picklist | 1 to 8 | סוג הגפן | new_gefen_type | | Payment | [Implementer] Numbered: long program names with codes. | YV20-247 | Built |
| Hebrew Year | Hebrew_Year__c | שנה עברית | Picklist | 5780 to 5789 | שנה עברית | new_hebrew_year | | General Info | [Implementer] Hebrew letters (תש"פ to תשפ"ט) are the translation. | YV20-247 | Built |
| Default Hotel | Default_Hotel__c | מלון (ברירת מחדל) | Picklist | 1 to 7 | מלון (ברירת מחדל) | new_hotel | | General Info | [Implementer] Numbered: hotel proper names. Question: the plan says hotels move to Event Hotel Allocation. Keep this field? | YV20-247 | Built |
| Location Classification | Location_Classification__c | סיווג לפי מיקום | Picklist | Online;In Person;Hybrid;Online Asynchronous | סיווג לפי מיקום | new_location_classification | | General Info | | YV20-247 | Built |
| Malam Code Expenses | Malam_Code__c | קוד מל"מ - הוצאות | Picklist | 152;1301;1501;1502;1701;1801;2101;2401;2501;2901;3001 | קוד מל"מ - הוצאות | new_malam_code | | Payment | [Implementer] The values are the codes themselves. | YV20-247 | Built |
| Participating Training Type | Participating_Training_Type__c | סוג משתלם | Picklist | Trainee;Haredi Trainee;Overseas Trainee | סוג משתלם | new_participating_training_type | | General Info | | YV20-247 | Built |
| Training Classification | Training_Classification__c | סיווג ההשתלמות | Picklist | 1 to 13 | סיווג ההשתלמות | new_training_classification | | General Info | [Implementer] Numbered: several values (עבודת מלחמה, מורשת מסע, מורשת הכנה) have no simple English translation. | YV20-247 | Built |
| Work Details Type | Work_Details_Type__c | סוג פרטי עבודה | Picklist | Teachers;Students;Police | סוג פרטי עבודה | new_work_details_type | | General Info | | YV20-247 | Built |
| Gregorian Year | Gregorian_Year__c | שנה לועזית | Picklist | 1995 to 2030 | שנה לועזית | new_year | | General Info | | YV20-247 | Built |
| Legacy ID | Legacy_ID__c | מזהה מערכת ישנה | Text(255), External ID | | | | | System Information | [Implementer] Added because every object gets `Legacy_ID__c` for the data load (00-CONVENTIONS, build rules). Not Unique, since no rule asks for it. | YV20-247 | To build |

### Picklist value translations

Ground rule (implementer, 25.11.2026): API values and labels are English on the field. Hebrew lives only in the object translation (Event__c-iw, one file per field).

**Event Type**

| API value and label | Hebrew (iw translation) |
|---|---|
| Professional Development | השתלמות |
| Study Day | יום עיון |
| Seminar | סמינר |
| Online Orders Tour | סיור מערכת הזמנות מקוונת |
| Training | הכשרה |
| Other | אחר |
| Conference | כנס |
| Event | אירוע |
| Show | הצגה |
| Spectacle | חיזיון |
| Open Tour | סיור פתוח |
| Course | קורס |

**Event Status**

| API value and label | Hebrew (iw translation) |
|---|---|
| New | חדש |
| Handling Completed | סיום טיפול |
| Cancelled | מבוטל |
| Pending Scheduling | ממתין לשיבוץ |
| Pending Orders Center Approval | ממתין לאישור מרכז הזמנות |
| Quote | הצעת מחיר |

**Payment Status**

| API value and label | Hebrew (iw translation) |
|---|---|
| None | ללא |
| Paid | שולם |
| Not Paid | טרם שולם |
| Paid and Fully Credited | שולם ובוצע זיכוי מלא |
| Paid and Partially Credited | שולם ובוצע זיכוי חלקי |
| Paid (Internal Settlement) | שולם (התחשבנות פנימית) |
| Not Paid (Internal Settlement) | טרם שולם (התחשבנות פנימית) |
| Place Reserved - No Payment Needed | שמירת מקום - אין צורך בתשלום |
| Awaiting Payment | ממתין לתשלום |
| No Payment Needed | אין צורך בתשלום |
| Payment Commitment | התחייבות לתשלום |
| Voucher Received | התקבל וואוצ'ר |
| Payment Failed | תשלום נכשל |
| Account Issued | הופק חשבון |
| Partially Paid | שולם חלקית |
| Account Cancelled | חשבון בוטל |
| Account Partially Issued | הופק חשבון חלקית |
| Transferred to Finance | הועבר לטיפול כספים |
| To Be Charged in Package | לחיוב בחבילה |

### Proposed Lightning Page section grouping

- **General Info:** Name, Event Type, Registration Product, Event Status, Payment Status.
- **Capacity:** Max Participants Per Event.
- **System Information:** Legacy ID, Created By, Last Modified By.

Record page, tab, Orders app entry and record types: not part of this task yet (implementer decision, 25.11.2026).

## Relationship Fields

One lookup built in this task: `Product__c` to `Product2` (standard, exists). No field is added to any other object.

### Relationships, later

Fields on other objects that point at `Event__c`. None are built here. `Event__c` must exist before any of them is deployed.

| On object | Field | Basis | Legacy field |
|---|---|---|---|
| `B25__Reservation__c` (GoMeddo) | Event lookup | Register rows 34 and 35: sessions and conference levels under the event | `new_activity_day.new_event_reservations` |
| `Registration__c` (not built) | Event lookup | Doc 3 3.2.3 | `new_event_registration.new_event_reservations` |
| `Coupon__c` (stub) | Event lookup, plus a junction for open tours | Doc 3 3.3.5 | `new_coupon.new_event_reservations` |
| Event Hotel Allocation, Event Shuttle (not built) | Event lookup | Register rows 44 and 45 | `new_hotel_reservation`, `new_shuttle` |
| `Event__c` itself | Linked Event, Linked Opportunity | Doc 3 3.2.1.1 | none |

The Bundle file already drops `EventReservations__c` on the Bundle (B14 in `02_Bundle-questions-for-Lior.md`), because the link runs from the event to the Bundle.

## Dropped legacy fields

None dropped in this task. Legacy fields of `new_event_reservations` that this task does not need are not listed (D-036); the proposed later fields are in [YV20-247_Plan.md](../../plan/YV20-247_Plan.md).

## Test records

None yet. Created by hand in `yv-partial` after the build.

| Record Name | Id | Task |
|---|---|---|

## Automations

None in this task. Later: the default capacity per event type, the automatic close at the registration deadline (A14), and event duplication (A14-09).
