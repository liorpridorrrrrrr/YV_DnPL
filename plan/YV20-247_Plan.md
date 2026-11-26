# YV20-247 plan: the Event object (Event__c)

| | |
|---|---|
| Task | YV20-247 (Jira, project YV20). The ticket text could not be read: the Atlassian connector was not signed in. Scope comes from the text the implementer pasted: "The event record: name, type, the product it sells (which sets the price), and status", plus the capacity paragraph of Doc 3 3.2.1. |
| Object | `Event__c`, label Event, Hebrew "אירוע" |
| Status | Draft, for review. Nothing is built or deployed. |
| Date | 25.11.2026 |
| Object file | [17_Event__c.md](../tickets/Data%20Migration/17_Event__c.md) |
| Draft metadata | `force-app/main/default/objects/Event__c/` (object and 6 custom fields) and `force-app/main/default/objectTranslations/Event__c-iw/` and `Event__c-en_US/` (translations, one file per field, as the other objects in the org). Not deployed. |
| Binding source | Doc 3 (approved 04.11.2026) 3.2 and 4.1 in `dd/doc3-events-finance/`; Doc 1 4.3; `knowledge/dd-corrections.md` C1, C2, C3; `knowledge/model-register.md` rows 31 to 37 |

## 1. Scope in three tiers

| Tier | What | Where it lives |
|---|---|---|
| Phase 1 | The ticket: name, type, product, status, plus capacity and payment status from Doc 3 3.2.1 | Section 3, object file, draft metadata |
| Phase 2 | Further event fields the DD names (registration rules, dates, budget, branding and others) | Section 4, proposal only; added task by task under D-036 |
| Later | Other epics: form switches, message texts, payment options, resource needs, Zoom, hotel and shuttle defaults, finance | Section 8 |

## 2. The object

| Property | Value |
|---|---|
| API name | `Event__c` |
| Label / plural | Event / Events |
| Hebrew label / plural | אירוע / אירועים |
| Name field | Text(80), label "Event Name", Hebrew "שם" (legacy `new_name`) |
| Description | `YV20-247 | Doc 3 3.2.1 | ...` (first token is the ticket key, as `CONVENTIONS.md` requires) |
| History tracking | On |
| Record types | Not in this task yet (implementer decision, 25.11.2026). Open question Q1 stays for later: Doc 3 chapters 5.3 to 5.6 name record types |
| Legacy entity | `new_event_reservations` ("ארועים מערכת אחודה", 117 fields) |
| Not touched | GoMeddo objects (managed), the live Cases module, standard `Event` (calendar). The names differ, but people may confuse them in reports and in the Orders app. |

## 3. Phase 1 fields

Types follow D-013: nvarchar to Text, int to Number(18,0), picklist to Picklist, lookup to Lookup. Translation uses the legacy Hebrew name exactly where a legacy field exists (`00-CONVENTIONS.md`). API names are proposals: they follow the legacy name without `new_`, with underscores as in the object files already built.

| # | Label | API name | Hebrew translation | Type | Legacy field (Old API Name) | DD source |
|---|---|---|---|---|---|---|
| 1 | Event Name | `Name` | שם | Text(80) | `new_name` | 3.2.1 |
| 2 | Event Type | `Event_Type__c` | סוג אירוע | Picklist (12 values) | `new_event_type` | 3.2.1, 4.1 |
| 3 | Registration Product | `Product__c` | מוצר ההרשמה | Lookup(Product2) | none | 3.2.1, C2, 5.5 |
| 4 | Event Status | `Event_Status__c` | סטטוס אירוע | Picklist (6 values, as in legacy) | `new_event_reservation_status` | 3.2.1; built 26.11.2026, English values |
| 5 | Payment Status | `Payment_Status__c` | סטטוס תשלום | Picklist (19 values) | `new_payment_status` | 3.2.1; built 26.11.2026, English values |
| 6 | Max Participants Per Event | `Max_Participants_Per_Event__c` | מקסימם משתתפים לאירוע | Number(18,0) | `new_max_participants_per_event` | 3.2.1, 3.2.5; built 26.11.2026 |
| 7 | Legacy ID | `Legacy_ID__c` | מזהה מערכת ישנה | Text(255), External ID | none | convention |

Rules from the text that are not fields on `Event__c`:
- **Price.** There is no price field. The price comes from the product's price book. Legacy `new_price` and `new_price_for_single_activity_day` are not carried over.
- **Per-session capacity.** Capacity for single sessions (course, series, show, spectacle) sits on the GoMeddo reservation, not here.
- **Status per activity day.** It sits on the GoMeddo reservation.
- **Required fields.** The DD requires capacity and the activity type. They are enforced by a validation rule and the page, not at database level, so the legacy load can run (Q2).
- **Products by type.** The products allowed for an event are usable only after the type is filled (Q3).
- **Sorting.** Activity days are shown sorted by date: a sort on the related list, not a field.

### Picklist values (English on the field; Hebrew only in the iw translation)

Ground rule (implementer, 25.11.2026): never create or edit an API value or label in Hebrew on a field or picklist. English only in the field metadata; Hebrew only in objectTranslations/<Object>-iw/. The Hebrew for each value is in the translation tables of [17_Event__c.md](../tickets/Data%20Migration/17_Event__c.md). Values come from the legacy PicklistValues column unless stated, translated to English.

- **Event Type (12):** Professional Development; Study Day; Seminar; Online Orders Tour; Training; Other; Conference; Event; Show; Spectacle; Open Tour; Course. The last four are from Doc 3 3.2.1; the legacy test value FOR TEST! is left out.
- **Event Status (7):** Draft; New; Handling Completed; Cancelled; Pending Scheduling; Pending Orders Center Approval; Quote. Draft is from the DD (A15-01); the rest are legacy.
- **Payment Status (19):** None; Paid; Not Paid; Paid and Fully Credited; Paid and Partially Credited; Paid (Internal Settlement); Not Paid (Internal Settlement); Place Reserved - No Payment Needed; Awaiting Payment; No Payment Needed; Payment Commitment; Voucher Received; Payment Failed; Account Issued; Partially Paid; Account Cancelled; Account Partially Issued; Transferred to Finance; To Be Charged in Package. All legacy values.

## 4. Phase 2: proposed fields (not in this task, for the next tasks)

Each is added through `/fields-for-task` with its own task and review. Translation is the legacy name where one exists.

Date and time fields use the three Salesforce types only: `Date` (no time), `DateTime` (date and time, stored in UTC, shown in the user's time zone) and `Time` (time of day only). In the field XML the element is `<type>Date</type>`, `<type>DateTime</type>` or `<type>Time</type>`. Legacy Dynamics `datetime` becomes `Date`, or `DateTime` when the name contains time or reminder (D-013). For the data load, values go in ISO 8601: Date `2026-11-25`, DateTime `2026-11-25T08:30:00.000Z`, Time `08:30:00.000Z`.

| Label | API name | Hebrew translation | Type | Legacy or DD source |
|---|---|---|---|---|
| Min Participants | `Min_Participants__c` | מינימום משתתפים | Number(18,0) | DD 3.2.5 |
| Max Participants Per Registration | `Max_Participants_Per_Registration__c` | מקסימום משתתפים לרישום בודד | Number(18,0) | `new_max_participants_per_registration`; V14-11; built 26.11.2026 |
| Is Named Registration | `Is_Named_Registration__c` | רישום שמי | Checkbox | DD 3.2.1, 5.1 |
| Registration Deadline | `Registration_Deadline__c` | מועד אחרון להרשמה | DateTime | DD 3.2.1 |
| Closed For Registration | `Is_Closed_For_Registration__c` | סגור לרישום | Checkbox | `new_is_closed_for_registration` |
| Registration For All Days | `Is_Registration_For_All_Days__c` | האם רישום לכל הימים | Checkbox | `new_is_registration_for_all_days` |
| Has Waiting List | `Has_Waiting_List__c` | רשימת המתנה | Checkbox | DD 3.2.5 |
| Registration Mode | `Registration_Mode__c` | אופן רישום | Picklist: Direct; Application Form | DD 5.3 |
| Schedule Structure | `Schedule_Structure__c` | מבנה הלו"ז | Picklist: Complex; Simple | DD 3.2.2, 5.3 |
| First Activity Day | `First_Activity_Day__c` | תאריך פעילות ראשון | Date | `new_first_activity_day`; V14-01 |
| Last Activity Day | `Last_Activity_Day__c` | תאריך פעילות אחרון | Date | `new_last_activity_day` |
| Start Time / End Time | `Start_Time__c`, `End_Time__c` | שעת פתיחה, שעת סיום | Time | DD 5.5 |
| Frequency | `Frequency__c` | תדירות | Picklist: Daily; Weekly; Every Two Weeks; Monthly; Manual | `new_frequency`; built 26.11.2026 |
| Day Of Week | `Day_Of_Week__c` | יום בשבוע | Picklist: Sunday; Monday; Tuesday; Wednesday; Thursday; Friday; Saturday | `new_day_of_week`; built 26.11.2026 |
| Recurrence Horizon (months) | `Recurrence_Horizon_Months__c` | אופק פתיחה בחודשים | Number(18,0) | DD 5.1 |
| Journey Start / End Date | `Journey_Start_Date__c`, `Journey_End_Date__c` | תאריך תחילת מסע, תאריך סיום מסע | Date | `new_journey_start_date`, `new_journey_end_date` |
| Duration Hours | `Duration_Hours__c` | אורך ההשתלמות (בשעות) | Number(18,0) | `new_duration_hours`; built 26.11.2026 |
| Format | `Format__c` | פורמט | Picklist: In Person; Zoom; Hybrid | DD 5.6 |
| Default Location | `Default_Location__c` | מיקום יום פעילות (ברירת מחדל) | Lookup(YV_Location__c) | `new_default_activity_day_location` |
| Default Resource | `Default_Resource__c` | משאב ברית מחדל | Lookup(B25__Resource__c) | `new_default_resource` (legacy typo kept) |
| Event Subject | `Event_Subject__c` | נושא הארוע | Lookup(Activity_Day_Subject__c) | `new_event_subject_lu` (legacy typo kept) |
| Target Audience | `Target_Audience__c` | קהל יעד | Picklist (values from legacy `new_age_group`) | `new_age_group`; DD 5.1; built 26.11.2026 as a local picklist, numbered 1 to 48 (many values have no simple English translation). The existing global value set `Age_Group` has the same 48 Hebrew values and is not used |
| Is Open To Public | `Is_Open_To_Public__c` | פתוח לקהל הרחב | Checkbox | DD 5.1 |
| Event Code | `Event_Code__c` | קוד אירוע | Text(255) | `new_event_code`; A14-09 prefix |
| Budget Framework | `Budget_Framework__c` | מסגרת תקציבית | Lookup(Budget_Framework__c) | `new_budget_framework`; DD 3.3.6 |
| Budget Section (manual) | `Budget_Section_Manual__c` | סעיף תקציבי (ידני) | Text(255) | `new_budget_section`; DD 5.4 |
| Financial Partner | `Financial_Partner__c` | שותף משלם | Lookup(Account) | `new_paying_institution`; DD 5.6 |
| Coordinator / Coordinator 2 | `Coordinator__c`, `Coordinator_2__c` | רכז אחראי ביד ושם, רכז אחראי ביד ושם - 2 | Lookup(Contact) | `new_yv_coordinator`, `new_yv_coordinator_2` |
| Subsidy Model | `Subsidy_Model__c` | מודל סבסוד | Picklist: Per Participant; Per Seminar | DD 5.4 |
| Subsidy Budget | `Subsidy_Budget__c` | תקציב סבסוד | Currency(16,2) | DD 5.3 |
| Study Material Cost Per Participant | `Study_Material_Cost_PP__c` | עלות חומרי לימוד למשתתף | Currency(16,2) | `new_study_material_cost_pp` |
| Study Material Cost Exception | `Is_Study_Material_Cost_Exception__c` | חריגה מאושרת | Checkbox | `new_is_study_material_cost_exception` |
| Price Cap | `Price_Cap__c` | תקרת מחיר | Currency(16,2) | DD 5.5 |
| Linked Event | `Linked_Event__c` | אירוע מקושר | Lookup(Event__c) | DD 3.2.1.1, 5.4 |
| Linked Opportunity | `Linked_Opportunity__c` | הזמנה מקושרת | Lookup(Opportunity) | DD 3.2.1.1 |
| Cancellation Date | `Cancellation_Date__c` | תאריך ביטול האירוע | Date | `new_cancellation_date` |
| Cancellation Reason | `Cancellation_Reason__c` | סיבה לביטול האירוע | Picklist (merge with the B25 list) | `new_cancellation_reason`; V14-03; built 26.11.2026 as a local picklist, numbered 1 to 20. The existing global value set `Cancel_Reason` has the same 20 Hebrew values and is not used |
| Cancelled By | `Cancelled_By__c` | שם מבטל האירוע | Lookup(User) | `new_cancelled_by` |
| Is Locked | `Is_Locked__c` | האם האירוע נעול | Checkbox | `new_is_event_reservation_locked` |
| Source | `Source__c` | מקור האירוע | Picklist (values to decide) | DD 5.2 |
| Needs Operation | `Needs_Operation__c` | דורש תפעול | Checkbox | DD 5.2, A15-01 |
| Marketing Review Status | `Marketing_Review_Status__c` | סטטוס בקרת שיווק | Picklist (values to decide) | DD 5.2, A15-04 |
| Show On Website | `Show_On_Website__c` | הראה באתר | Checkbox | `new_show_on_website`; A14-08 |
| Attendance Management Enabled | `Attendance_Management_Enabled__c` | ניהול נוכחות שמי | Checkbox | DD 5.7 |
| Education Ministry Symbol | `Education_Ministry_Symbol__c` | סמל השתלמות במשרד החינוך | Text(255) | DD 5.6 |
| Region | `Region__c` | אזור | Picklist (source to decide) | DD 5.6; legacy `new_district` is a candidate; the legacy `new_district` was built separately as `Geographic_District__c`, so this row stays open (DD 5.6) |
| Additional Participants | `Additional_Participants__c` | מספר מבקרים נוספים | Number(18,0) | `new_num_additional_participants`; DD 5.5; built 26.11.2026 |
| Total Participants | `Total_Participants__c` | סה"כ נרשמים | Number(18,0) | `new_total_participants`; built 26.11.2026 as a plain number, calculation later (Q11) |
| Divisions For Registration | `Divisions_For_Registration__c` | כמות חטיבות לרישום | Number(18,0) | `new_divisions_for_registration`; built 26.11.2026 |
| Coupon Discount | `Coupon_Discount__c` | הנחת קופון | Number(18,2) | `new_coupon_discount`; built 26.11.2026 as Number, not Currency or Percent, until the coupon task decides amount vs percent |
| Exchange Rate | `Exchange_Rate__c` | שער המרה | Number(16,2) | `exchangerate` (Dynamics system field); built 26.11.2026 at Etti's request, 2 decimals as chosen (legacy may hold more) |
| Discount Type | `Discount_Type__c` | סוג הנחה | Picklist, numbered 1 to 5 | `new_discount_type`; built 26.11.2026; numbered because "משואה לתקומה" has no simple English translation |
| Geographic District | `Geographic_District__c` | מחוז גאוגרפי | Picklist, numbered 1 to 18 | `new_district`; built 26.11.2026; numbered, values mix districts and school sectors |
| Training Type | `Training_Type__c` | סוג השתלמות | Picklist: Haredi; Regular; Colleges | `new_training_type`; built 26.11.2026 |
| Gefen Type | `Gefen_Type__c` | סוג הגפן | Picklist, numbered 1 to 8 | `new_gefen_type`; built 26.11.2026; numbered, long program names with codes |
| Hebrew Year | `Hebrew_Year__c` | שנה עברית | Picklist: 5780 to 5789 | `new_hebrew_year`; built 26.11.2026; Hebrew letters as the translation (תש"פ to תשפ"ט) |
| Default Hotel | `Default_Hotel__c` | מלון (ברירת מחדל) | Picklist, numbered 1 to 7 | `new_hotel`; built 26.11.2026; numbered, proper names. The DD replaces hotels with Event Hotel Allocation, so confirm it stays |
| Location Classification | `Location_Classification__c` | סיווג לפי מיקום | Picklist: Online; In Person; Hybrid; Online Asynchronous | `new_location_classification`; built 26.11.2026. Not the same as Format (In Person; Zoom; Hybrid) |
| Malam Code Expenses | `Malam_Code__c` | קוד מל"מ - הוצאות | Picklist: 11 numeric codes | `new_malam_code`; built 26.11.2026; values are the codes |
| Participating Training Type | `Participating_Training_Type__c` | סוג משתלם | Picklist: Trainee; Haredi Trainee; Overseas Trainee | `new_participating_training_type`; built 26.11.2026 |
| Training Classification | `Training_Classification__c` | סיווג ההשתלמות | Picklist, numbered 1 to 13 | `new_training_classification`; built 26.11.2026; numbered, several values have no simple translation |
| Work Details Type | `Work_Details_Type__c` | סוג פרטי עבודה | Picklist: Teachers; Students; Police | `new_work_details_type`; built 26.11.2026 |
| Gregorian Year | `Gregorian_Year__c` | שנה לועזית | Picklist: 1995 to 2030 | `new_year`; built 26.11.2026 |
| Internal Name | `Internal_Name__c` | שם לשימוש פנימי | Text(255) | `new_internal_name` |
| Subtitle | `Subtitle__c` | תת כותרת | Text(255) | `new_subtitle` |
| Description | `Description__c` | תקציר | Long Text Area(32000) | `new_description` |
| Conference Logo | `Logo__c` | לוגו הכנס | Type to decide (URL or file) | DD 5.3 |
| Title / Description for Google | `Title_For_Google__c`, `Description_For_Google__c` | כותרת לגוגל, תיאור לגוגל | Text(255) | `new_title_for_google`, `new_description_for_google` |
| Title for Social | `Title_For_Social__c` | כותרת לרשתות חברתיות | Text(255) | `new_title_for_social` |
| Description for Social | `Description_For_Social__c` | תיאור לרשתות חברתיות | Text Area (255) | `new_description_for_social`; Text Area, not Text, so users can enter line breaks and size the box on the layout (Etti, 26.11.2026) |
| Is Google Indexed | `Is_Google_Indexed__c` | אינדוקס בגוגל | Checkbox | `new_is_google_indexed` |
| Registration Form | `Registration_Form__c` | טופס הרשמה | Target undefined | DD 3.2.1 |

Picklist value rules (26.11.2026): API value and label are English where each value has a simple English translation. If any value in a field has none, the whole field uses 1, 2, 3 and so on for value and label. The Hebrew text lives only in the iw translation, with `"` written as `&quot;`.

Also Phase 2, from the DD, not fields: conference type (ישראלי, בינלאומי, חרדי) and seminar type (סגור, פתוח, מסע) are one subtype picklist or two (Q1).

## 5. Relationships to Event__c

Lookup fields on other objects that point at `Event__c` are listed in [17_Event__c.md](../tickets/Data%20Migration/17_Event__c.md), "Relationships, later". `Event__c` is built first.

## 6. Screens, LWC and displays that involve Event__c

"Type" is what I expect to build: a record page, a Lightning web component (LWC), a flow or screen, a form, a report, or a display setting. None exists yet. The DD says screens are described by structure and components, not by field position (Doc 3 chapter 4).

| # | Screen or display | Type | What it uses from Event__c | DD source |
|---|---|---|---|---|
| 1 | **Event screen** (event record page): type, dates, capacity, statuses, budget framework, branding, linked registration form. Dynamic form by event type, so a conference shows different fields than a show. | Lightning record page with dynamic forms, `Order-Event- Record Page` | Almost every field | Doc 3 4.1; Doc 1 4.3 |
| 2 | **Related lists on the event screen:** sessions (GoMeddo reservations), registrations, linked events, logistics items (registration items, hotel allocations, shuttles) | Related lists, or a dedicated component | Lookups from the other objects to the event | Doc 3 4.1 |
| 3 | **Quick actions:** build schedule, open groups, duplicate event, produce outputs | Quick actions (flows) | Event record, A14-09 (copy with all data; days without dates; the record is created on save) | Doc 3 4.1, A14-09, A18-11 |
| 4 | **Schedule tree** (complex events): three levels, division, unit, group, with occupancy per level and marks for "shown in the form" and "for everyone" | LWC (custom tree) | Schedule structure, capacity, GoMeddo reservations under the event | Doc 3 3.2.2, 4.1 |
| 5 | **Simple event view:** division into groups only | Same record page, different component visibility | Schedule structure | Doc 3 4.1 |
| 6 | **Event control center:** all events by row and by date, real-time occupancy, daily, weekly and monthly views; public events and closed events in different colors; filters by event type, department and activity place | LWC (calendar or board) | Type, dates, capacity, open to public, default location | Doc 3 4 (control center) |
| 7 | **Registration screen and registrant management:** list per event, filter by status, contact type and schedule level, single or bulk status update, waiting-list promotion, manual placement into schedule levels, proactive messages | Screen (LWC or list view with actions), reached from the event | Event, registrations | Doc 3 4.2 |
| 8 | **Message from the event record:** to registrants, coordinators and assigned service providers; attach the schedule; the message is logged on the event | Quick action and flow | Event record | A14-13, A18-08 |
| 9 | **Consolidated financial report from the event** | Report run from the event | Event, assignments | A14-14 |
| 10 | **Live view of customer accounts** (from the finance system) from the event | Component, no stored data | Event, customer | Doc 3 4.3, A21-02 |
| 11 | **Scheduling board** (לוח השיבוץ) and assignment from the event itself, where the detailed schedule is shown | Existing screen of the orders module (Doc 2) | Event, sessions | Doc 2 chapter 11; A14-06 |
| 12 | **Public registration forms** (Titan Forms): long and short event registration, conference forms 1 and 2, seminar application, course or journey form. A closed event is removed from the offer and the form. | External forms, fed by Salesforce records | Event, status, closed flag, product and price | Doc 3 chapter 7; A14-08 |
| 13 | **Website and online display:** shown, shown as full, hidden (per session); "show on website" flag; the unified products page after marketing approval | Display setting, manual upload by marketing | Show On Website, open to public | A14-08, A15-04 |
| 14 | **Smartticket sync:** event record created from the ticketing system; draft until scheduling is complete; "needs operation" mark; restricted-audience events not shown online | Integration, no screen | Source, status, needs operation, marketing status | Doc 3 5.2, A15-01 to A15-06 |
| 15 | **Kiosk:** shows the day's events open for registration | Kiosk form (Titan Forms) | Event, dates, status, open to public | Doc 3 4 (kiosk), 5.8 |
| 16 | **Attendance screens:** mark arrival and QR on the session; named attendance is enabled per event | GoMeddo plus a flag on the event | Attendance management flag | Doc 3 5.7 |
| 17 | **Reports (10):** events detailed, events brief, event activity components analysis, trainings by Hebrew year, trainings by budget framework, trainings by date range, study days summary, service provider assignment in events, shows and spectacle, attendance by event and session | Reports on Event__c, with the registration, session and budget framework objects | Many | Doc 3 6 |
| 18 | **Dashboards:** events and registration trends (monthly over the year, by year) and the events and finance dashboards | Dashboards | Dates, type, status | Doc 3 6 (dashboards), tender row 234 |
| 19 | **Coupon record:** shows the linked event or events | Related list on the coupon | Event lookup from the coupon | Doc 3 3.3.5 |
| 20 | **Budget framework record:** events that use it; separate use for orders and for events | Related list, filter | Budget Framework lookup | Doc 3 3.3.6 |
| 21 | **Opportunity (order and registration):** the event it came from; linked event and order seen from both sides | Related list or field | Linked Opportunity, linked event | Doc 3 3.2.1.1 |
| 22 | **Confirmation and reminder messages:** the template is chosen by event type | Flow, email templates | Event type | A18-05, A14-05 |
| 23 | **Home page and per-type screens:** today's orders and events, and a screen per event type | Home page, list views, or a record page per record type | Type, dates | Doc 2 chapter 11 (review comment c86) |

## 7. Open questions

Tags follow D-029. None blocks the Phase 1 build except the org check.

| # | Tag | Question |
|---|---|---|
| Q1 | [Lior] | ON HOLD (implementer, 25.11.2026): not decided and not worked on in this task. Doc 3 chapters 5.3 to 5.6 name record types (conference, seminar, course, training), while Doc 1 and 5.1 use an event-type field. Record types, a picklist, or both? Also: are conference type and seminar type one subtype field? |
| Q2 | [Lior] | Required capacity and activity type: validation rule plus page, not required at database level, so blank legacy values can load. Confirm. |
| Q3 | [Lior] | "Activity type" (required, gates the products) and "event type": the same field? If yes, a rule on the product lookup applies, and it is a later automation. |
| Q4 | [Lior] | The default capacity per event type: custom metadata or a type object? The same object could hold the event-number prefix (A14-09) and the "open to public" default. |
| Q5 | [Lior] | Event Status values: the legacy list reads like order statuses. Which statuses does an event need? |
| Q6 | [Anna] | Payment Status: 19 legacy values, several overlapping the financial account match status. Keep all? |
| Q7 | [Lior] | Event Type values: is "סיור מערכת הזמנות מקוונת" the same as "סיור פתוח"? |
| Q8 | [Lior] | Coupon to event: a lookup on the coupon for one event, plus a junction for open tours that link one coupon to several events? |
| Q9 | [Lior] | Budget framework: add a "for events" flag on `Budget_Framework__c` (built by Tal, so by pull request tagged to Tal), or treat seminar and training frameworks as event frameworks? |
| Q10 | [Lior] | Registration form: no entity in the DD. A lookup, or chosen by event type? |
| Q11 | [Lior] | Occupancy (Total Participants): a roll-up cannot sit on a plain lookup from `Registration__c`. A flow, a count, or master-detail? |
| Q12 | [Lior] | Coordinators: Contact (Guide record type), as in legacy, or User? |
| Q13 | [Implementer] | Language and country lookups: no Language object in `force-app`. Check the org. |
| Q14 | [Implementer] | Cancellation reason: B25 already has a `Cancel_Reason__c` picklist, so the merge-values rule applies. |
| Q15 | [Neev] | The GoMeddo target for the legacy event item (שיבוצים), and whether GoMeddo supports three nesting levels (open item C1). |

## 8. Later: legacy fields held back (other epics)

- **Form switches:** `new_is_show_*`, `new_is_*_required`, `new_work_details_type` (registration form task).
- **Message texts:** confirmation, closed registration, deferred payment, signature, bold texts (communications task). Recommended Salesforce types, decided with Etti 26.11.2026 (legacy `ntext` becomes Long Text Area(32000); legacy `nvarchar` becomes Text Area (255) for sentences):
  - Long Text Area(32000): `new_free_text_closed`, `new_free_text_confirmation`, `new_free_text_for_email`, `new_signature`, `new_text_confirmation_payment_later`, `new_text_for_email_payment_later`, `new_internal_comments`, `new_yv_comments`.
  - Text Area (255): `new_bold_text_confirmation_payment`, `new_bold_text_confirmation_payment_later`, `new_description_payment_later`, `new_description_payment_later_bold`, `new_opening_comment`.
  - Check the maximum length of the `nvarchar` values in the legacy data first. Anything over 255 moves to Long Text Area.
  - Open: whether "bold" means formatting (Rich Text Area) or only emphasis done in the template.
- **Payment options:** `new_is_allow_payments`, `new_is_allow_telephone_payment`, `new_is_deferred_payment`, `new_coupon_*`, `new_discount_type` (finance and coupons).
- **Resource needs:** `new_is_museum_required`, `new_is_campus_required`, `new_is_big_questions_required`, `new_is_need_event_schedule` (resource scheduling).
- **Contact details and email:** `new_*_contact`, `new_from_email`. Contact name, email and phone: no field on `Event__c`; the record page shows them from the linked Contact (Neev's rule for the order, 19.10.2026, see `06_Opportunity.md`), unless a valuable reason is shown. Before accepting, compare the legacy values with the Contact's own on a sample. `new_from_email`: Email type, check Org-Wide Email Addresses first.
- **Zoom:** `new_is_zoom_registration`, `new_zoom_meeting_number` (Text, not Number, so leading zeros survive), `new_is_send_link_in_zoom_mail`.
- **Replaced by new objects:** `new_hotel` (also built as the picklist `Default_Hotel__c`, to confirm), `new_supplier` (Event Hotel Allocation and Event Shuttle, register rows 44 and 45).
- **System fields:** `transactioncurrencyid`. (`exchangerate` is built as `Exchange_Rate__c`, see section 4.)
- **Not clear:** `new_is_claims_donor`, `new_petty_cash`, `new_is_tuition` (`new_gefen_type`, `new_malam_code`, `new_year`, `new_hebrew_year` are now built, see section 4) (candidate for a later task or a drop; ask Anna).

## 9. Work steps

1. Review this plan and answer Q1 to Q15 as they come up. The Phase 1 build does not wait for them (D-029), except where a field is marked open.
2. Authorize the sandbox: `sf org login web --instance-url <sandbox My Domain URL> --alias yv-partial` (see `sources/ORGS.md`). Always pass `--target-org yv-partial`.
3. Clash check, read-only (`CONVENTIONS.md`):
   - `sf sobject describe --sobject Event__c --target-org yv-partial` (expect "not found")
   - `sf org list metadata --metadata-type CustomObject --target-org yv-partial`
   - `sf sobject describe --sobject Product2 --target-org yv-partial` (no existing event lookup)
   Record the result in the object file under "Components".
4. Review the draft metadata in `force-app/main/default/objects/Event__c/` and `force-app/main/default/objectTranslations/Event__c-iw/`. Then validate with `--dry-run` before any real deploy, to `yv-partial` only (D-035). Never production, never `sf data`.
5. After the build: create a test record, set the field rows to `Built`, and fill the "Test records" list. The record page, tab, Orders app entry and record types are not part of this task yet (implementer decision, 25.11.2026).
6. Branch `task/YV20-247` (the current branch is `YV20-247`), push, open a pull request into `main`, and merge it yourself (D-036).
7. Write the decisions that affect other objects into `knowledge/model-register.md` and tell their implementers (Coupon, Budget Framework, B25 reservation).

## 10. Status, time log and blockers

(Moved here from the sync file that was drafted for this task. If the team's checks need a `sync/DM-<name>.md`, copy this section there.)

| Key | Status | Category | Components actually built |
|---|---|---|---|
| YV20-247 | Plan and draft metadata written, waiting for review. Not built: `yv-partial` is not authorized on this machine. | Fields | None (draft metadata only, `force-app/main/default/objects/Event__c/`) |

| Date | Ticket | Start | End |
|---|---|---|---|
| 25.11.2026 | YV20-247 | (fill in) | (fill in) |

Blocked:
- YV20-247, build: `yv-partial` login needed (`sf org login web --alias yv-partial`) for the clash check in `CONVENTIONS.md`.
- YV20-247, Jira text not read: the Atlassian connector was not signed in. Scope taken from the text pasted by the implementer.

Open Issues raised: none yet. Open questions are in section 7 and in the Questions column of `tickets/Data Migration/17_Event__c.md`.
