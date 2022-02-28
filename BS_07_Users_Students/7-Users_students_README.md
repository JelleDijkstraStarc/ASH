# BS_07_Users_Students

Powerschool &rarr; BrightSpace CSV Student Export for 07-Users

**PROVIDES FIELDS:**

- `org_defined_id` used in [08-Enrollments_Students](../BS_08_Enrollments_Students/8-Enrollments_students_README.md) as `child_code` 

|Field |Format |example |
|:-|:-|:-|
|`org_defined_id`| `S_`_`STUDENTS.STUDENT_NUMBER`_ | S_123456

**USES FIELDS:**

- none

## Data Export Manager

- **Category:** Show All
- **Export Form:**  com.txoof.brightspace.students.usernames

### Lables Used on Export

| Label |
|-|
|type|
|action|
|username|
|org_define_id|
|first_name|
|last_name|
|password|
|role_name|
|relationships|
|pref_frist_name |
|fref_last_name |

### Export Summary and Output Options

#### Export Format

- *Export File Name:* `7-Users.csv`
- *Line Delimiter:* `CR-LF`
- *Field Delimiter:* `,`
- *Character Set:* TBD

#### Export Options

- *Include Column Headers:* `True`
- *Surround "field values" in Quotes:* TBD

## Query Setup for `named_query.xml`

### PowerQuery Output Columns

| header | table.field | value | NOTE |
|-|-|-|-|
|type| STUDENTS.ID | user | N1 |
|action| STUDENTS.ID | UPDATE | N1 |
|username| U_STUDENTSUSERFIELDS.EMAILSTUDENT | _bar@ash.nl_ |
|org_define_id| STUDENTS.STUDENT_NUMBER | _S\_123456_ |
|first_name| STUDENTS.FIRST_NAME | _Jane_ |
|last_name| STUDENTS.LAST_NAME |_Doe_ | 
|password| STUDENTS.ID | '' | N1 |
|role_name| STUDENTS.ID | _Learner_ | N1 |
|relationships| STUDENTS.ID | TBD | N1 |
|pref_frist_name| STUDENTS.ID |TBD | N1 |
|pref_last_name| STUDENTS.ID |TBD | N1 |

#### Notes

**N1:** Field does not appear in database; use a known field such as `<column column=STUDENT.ID>header<\column>` to prevent an "unknown column error"

### Tables Used

| Table |  |
|-|-|
|STUDENTS| |
|U_STUDENTSUSERFIELDS| |

### SQL

```
select 
    'user' as "type",
    'UPDATE' as "action",
    REGEXP_REPLACE(U_STUDENTSUSERFIELDS.EMAILSTUDENT, '(^.*)(@.*)', '\1') as "username",
    /* use student number as unique id - we recycle email addresses and this causes issues */
    /* prepend a 'S" to the number to distinguish between students & teachers */
    'S_'||STUDENTS.STUDENT_NUMBER as "org_defined_id",
    STUDENTS.FIRST_NAME as "first_name",
    STUDENTS.LAST_NAME as "last_name",
    '' as "password",
    (CASE STUDENTS.ENROLL_STATUS
        WHEN 0 THEN 1
        ELSE NULL END) as "is_active",
    'Learner' as "role_name",
    U_STUDENTSUSERFIELDS.EMAILSTUDENT as "email",
    '' as "relationships",
    '' as "pref_first_name",
    '' as "pref_last_name"
 from U_STUDENTSUSERFIELDS U_STUDENTSUSERFIELDS,
    STUDENTS STUDENTS 
 where STUDENTS.DCID=U_STUDENTSUSERFIELDS.STUDENTSDCID
    and STUDENTS.ENROLL_STATUS =0
    and STUDENTS.GRADE_LEVEL >=5
 order by STUDENTS.GRADE_LEVEL ASC, STUDENTS.LAST_NAME ASC
```