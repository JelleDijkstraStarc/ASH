# BS_04_Templates

Powerschool &rarr; BrightSpace CSV active department-templates for 04-Templates

**PROVIDES FIELDS:**

- `code` used in 5-Ofering as `template_code` 

|Field |Format |example |
|:-|:-|:-|
|`code`| `TEMPLATE_`_`CC.SCHOOLID`_`_`_`COURSES.SCHED_DEPARTMENT`_| TEMPLATE_2_MSEAL

**USES FIELDS:**

- `code` from [2-Other](../BS_02_Departments/2-Departments_README.md) as `department_code`

## Data Export Manager

- **Category:** Show All
- **Export Form:**  com.txoof.brightspace.courses.templates
- 
### Lables Used on Export

| Label |

| Label |
|-|
|type|
|action|
|code|
|name|
|start_date|
|end_date|
|is_active|
|department_code|
|template_code|
|semester_code|
|offering_code|
|custom_code|

### Export Summary and Output Options

#### Export Format

- *Export File Name:* `4-Template-%d.csv`
- *Line Delimiter:* `CR-LF`
- *Field Delimiter:* `,`
- *Character Set:* UTF-8

#### Export Options

- *Include Column Headers:* `True`
- *Surround "field values" in Quotes:* TBD

## Query Setup for `named_queries.xml`

### PowerQuery Output Columns

| header | table.field | value | NOTE |
|-|-|-|-|
|type| CC.ID | _department_ | N1
|action| CC.ID |_UPDATE_ | N1
|code| COURSES.SCHED_DEPARTMENT | _Templ_3\_HSPerArts_ |
|name| COURSES.SCHED_DEPARTMENT | _HSPerArts_ |
|start_date| CC.ID | '' | N1
|end_date| CC.ID | '' | N1
|is_active| CC.ID | '' | N1
|department_code| COURSES.SCHED_DEPARTMENT | '' | N1
|template_code| CC.ID | '' | N1
|semester_code| CC.ID | '' | N1
|offering_code| CC.ID | '' | N1
|custom_code| CC.ID | '' | N1

#### Notes

**N1:** Field does not appear in database; use a known field such as `<column column=STUDENT.ID>header<\column>` to prevent an "unknown column error"

### Tables Used

| Table |
|-|
|STUDENTS|
|CC|
|COURSES|

### SQL

```SQL
select distinct
    'department' as "type",
    'UPDATE' as "action",
    'Templ_'||CC.SCHOOLID||'_'||COURSES.SCHED_DEPARTMENT as "code",
    COURSES.SCHED_DEPARTMENT as "name",
    '' as "start_date",
    '' as "end_date",
    '' as "is_active",
    CC.SCHOOLID||'_'||COURSES.SCHED_DEPARTMENT as "department_code",
    '' as "template_code",
    '' as "semester_code",
    '' as "offering_code",
    '' as "custom_code"
 from COURSES COURSES,
    STUDENTS STUDENTS,
    CC CC,
    SCHOOLS SCHOOLS
 where CC.STUDENTID=STUDENTS.ID
    and CC.SCHOOLID=SCHOOLS.SCHOOL_NUMBER
    and CC.COURSE_NUMBER=COURSES.COURSE_NUMBER
    and CC.TERMID >= case 
            when (EXTRACT(month from sysdate) >= 1 and EXTRACT(month from sysdate) <= 7)
            THEN (EXTRACT(year from sysdate)-2000+9)*100
            when (EXTRACT(month from sysdate) > 7 and EXTRACT(month from sysdate) <= 12)
            THEN (EXTRACT(year from sysdate)-2000+10)*100
            end
    and length(COURSES.SCHED_DEPARTMENT) > 0
 order by "code" asc
```