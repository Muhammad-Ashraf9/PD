# XML and Allied Technologies

> [!tip] XML
>
> - XML: Extensible Markup Language
> - XML is a markup language used to store and transport data in structured formats.

> [!faq] why XML?
>
> - instead of DBMS, XML is used to store ==small== amount data in a structured format.
> - can save configuration data in XML format.
> - no need to install DBMS.
> - save word document data(font, size, color, etc) in XML format. (docx)
> - docx: load XML data (documnet.xml, font.xml, etc) and show it in word format.
> - import and export data from different DBMS (sql server,mysql) using XML.

---

> [!danger] Course outline
>
> 1. XML(How to write XML) and DTD(validation)
> 2. XML Schema (validation)
> 3. XSL

## XML

> [!tip] All tags in HTML are optional(empty HTML is valid HTML)

> [!tip] XML tags does not have style meaning. (HTML tags have style meaning)
>
> - XML tags are USER DEFINED tags. (HTML tags are PREDEFINED tags)
> - these tags needs to have meaning to the user. (`<student>`:to store student data, `<book>`: to store book data, etc)

```xml
<student>
    <name>John</name>
    <age>20</age>
  </student>
```

> [!tip] `.xml` is the extension of XML file.

```xml
<!-- array of students -->
<!-- the following is not well formed XML  (one root element)-->
<!-- need the id of second student -->
  <!-- can be done using DBMS using query but in small data we can use XML -->
  <student>
    <name>John</name>
    <age>20</age>
  </student>
  <student>
    <name>Smith</name>
    <age>21</age>
  </student>
```

> [!bug] Empty XML is not well formed XML
>
> - xml file have ==well formed rules==.
>   > [!example] edit project configuration file in C# correctly for example.

> [!tip] 6 rules of well formed XML
>
> - Parser: software that checks the xml file against the rules.
> - There are 2 types of parser:
> - 1. non-validator parser: checks the xml file against the 6 rules.(Browser:open xml file) gives error if not well formed.
> - 2. validator parser: checks the xml file against DTD or XML Schema.(need installation)

> [!done] XML Altova spy
>
> - has anything related to XML.
> - we only need Red icon (XML Spy)
> - https://www.altova.com/xmlspy-xml-editor/download

> [!tip] non-validator parser
>
> - input xml file
> - output: error if not well formed depending on the 6 rules.
> - example: browser

> [!warning] 6 rules of well formed XML
>
> 1. only one root element
> 2. any opening tag must have a closing tag.
> 3. tags are case sensitive.
> 4. any attribute value must be enclosed in double quotes.
> 5. you cannot write the same attribute twice in the same tag.
> 6. NO overlapping tags.

> [!bug] having 2 id tags is ==well formed== but ==not valid== xml
>
> - validation from Business point of view.
>
> - to validate against business rules we need DTD or XML Schema.
>
> ```xml
> <!-- DTD -->
>  <!ELEMENT student (id,name)><!-- id and name are child elements of student -->
>  <!ELEMENT id (#PCDATA)> <!-- PCDATA: plain text -->
>  <!ELEMENT name (#PCDATA)>
> ```
>
> > [!done] Any valid xml is well formed xml but not vice versa.

```xml
<!-- for the first 2 rules: well formed -->
<student>
<id>1</id>
<!-- <id>1</id> not valid xml(only one id as validation but well formed)  -->
<!-- not all well formed xml are valid xml -->
    <name>
        John
    </name>

</student>
```

---

##### XML Structure

> [!tip] `.xml` file structure
>
> - prolog section: optional
>   - to link to DTD or XML Schema
> - content section: required: 6 rules
>   - content: data
>     > [!danger] rules
>     >
>     > - should have only one root element

> [!tip] 6 rules of well formed XML
>
> - minimum requirement to deal with XML file.
>
> 1. only one root element
> 2. any opening tag must have a closing tag.
> 3. tags are case sensitive.
> 4. any attribute value must be enclosed in double quotes.
> 5. you cannot write the same attribute twice in the same tag.
> 6. NO overlapping tags.
>    additional rule:
>
> - xml declaration has to be in the ==first line==.

> [!examp] css can be applied to any Markup language (HTML, XML, etc)

```xml
<!-- iti.xml -->

<iti>
    <student>
        <id>1</id>
        <name>John</name>
        <!-- <img src=""> error: as it is not closed -->
        <!-- <img src=r.jpg/> error: as it is not enclosed in double quotes -->
        <!-- <img src="r.jpg" src="r.jpg"/> error: as it is repeated -->
        <!-- <b> <i>
        iti
        </b></i> overlapping tags -->
        <!-- those can pass in HTML but not in XML -->
    </student>
    <student>
        <id>2</id>
        <name>Smith</name>
    </student>
</iti>
```

> [!warning] XML declaration
>
> - optional (in prolog section)
> - ```xml
>   <?xml version="1.0" encoding="UTF-8"?>
>   ```
>
> - xml is language independent (can be in any language)
> - using encoding we can specify the language of the xml file.(optional)
>   > [!danger] empty xml is not well formed xml
>   >
>   > - xml declaration has to be in the ==first line==.

> [!tip] Altove spy
>
> - `f7` check well formed

```xml
<!-- iti.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<iti>

</iti>
<mcit><!-- error at this line: only one root element -->

</mcit>
```

```xml
<!-- iti.xml -->
<!-- Errors -->
<?xml version="1.0" encoding="UTF-8"?>
<iti>
    <!-- <pD >

    </pD> -->
    <!-- <pd branch=mansoura>

    </pd> -->
    <!-- <pd branch=mansoura branch=hehe>

    </pd> -->
    <!--               -->
    <!-- <pd>
        <student>
        <id>
        </student>
        </id>
    </pd> -->
</iti>

```

```xml
<!-- iti.xml -->
<!--well formed -->
<!-- not valid -->
<?xml version="1.0" encoding="UTF-8"?>

<iti>
    <pd branch="mansoura">
        <student>
            <id>1</id>
            <name>John</name>
        </student>
        <student>
            <id>2</id>
            <id>3</id><!-- not valid -->
            <name>Smith</name>
        </student>
    </pd>
```

> [!tip] we need to apply business rules to the xml file.
>
> - the first element under student is id.
> - the second element under student is name.

> [!danger] Not allowed characters in tag names:
> all letters are allowed a-z A-Z,\_,.,-,digits 0-9
> - you can start with a letter or `_` and continue with any of the allowed characters.
> - you cannot start with a digit or `-` or `.`
> space,!,$

```xml
<!-- iti.xml -->
<iti>


</iti>
```
