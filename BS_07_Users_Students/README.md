# BS_07_Users_Students

Powerschool &rarr; BrightSpace CSV Student Export for 07-Users

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
|username| U_STUDENTSUSERFIELDS.EMAILSTUDENT |_ASH email userid_ |
|org_define_id| STUDENTS.ID | _SIS student number_ |
|first_name| STUDENTS.FIRST_NAME | _SIS First Name_ |
|last_name| STUDENTS.LAST_NAME |_SIS Last Name_ | 
|password| STUDENTS.ID |_NONE_ | N1 |
|role_name| STUDENTS.ID | Learner | N1 |
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
    STUDENTS.ID as "org_defined_id",
    STUDENTS.FIRST_NAME as "first_name",
    STUDENTS.LAST_NAME as "last_name", 
    '' as "password",
    'Learner' as "role_name",
    '' as "relationships",
    '' as "pref_first_name",
    '' as "pref_last_name",
    U_STUDENTSUSERFIELDS.EMAILSTUDENT as "email",
    (CASE STUDENTS.ENROLL_STATUS
        WHEN 0 THEN 1
        ELSE NULL END) as "is_active"
        
from STUDENTS STUDENTS,
    U_STUDENTSUSERFIELDS U_STUDENTSUSERFIELDS
where STUDENTS.ENROLL_STATUS =0
    and U_STUDENTSUSERFIELDS.STUDENTSDCID=STUDENTS.DCID
    and STUDENTS.GRADE_LEVEL >=5
    order by STUDENTS.GRADE_LEVEL asc
```