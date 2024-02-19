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
>
> - you can start with a letter or `_` and continue with any of the allowed characters.
> - you cannot start with a digit or `-` or `.`
>   space,!,$

```xml
<!-- iti.xml -->
<iti>


</iti>
```

---

### Break

---

### DTD

> [!tip] DTD: Document Type Definition
> elements(tags)
>
> - naming, content, repaeting number for tags ,attributes,

> [!example] Tags(Elements declaration)in DTD
> use Directive `<!ELEMENT>` to declare elements
>
> - `<!ELEMENT tagName (content)>`
>
> ```xml
> <!ELEMENT student (id,name)> <!-- we have to specify the content of the tag we don't know anything about id ,name-->
> <!ELEMENT id (#PCDATA)> <!-- PCDATA: plain text -->
> <!ELEMENT name (#PCDATA)>
> ```
>
> - id and name need to be declared after student. (order matters)
>   to be id or name (use `|` or)
>
> ```xml
> <!ELEMENT student (id|name)>
> ```

> [!bug]
>
> - grammar: is content of the tag
> - #PCDATA: parsed character data (for tags not for attributes)
> - `>` and `<` has meaning in XML so we cannot use them in the content of the tag. we use `&gt;` and `&lt;` instead. like HTML.
> - #CDATA: character data (for attributes not for tags)

> [!example] Existance or Repaeting for elements
>
> - `,` and keep sequence
> - `|` or
> - `?` zero or one
> - `*` zero or more
> - `+` one or more

```dtd
<!ELEMENT student (id,phone+)>
<!-- phone is required and can be more than one -->
<!ELEMENT student (id,phone?)>
<!-- phone is optional -->
<!ELEMENT Person (id,Phone)+>
<!-- Person can have one or more id and Phone -->
```

---

```dtd
<?xml version="1.0" encoding="UTF-8"?>
<!ELEMENT students ( student )>
<!ELEMENT student ( id,name,email )>
<!ELEMENT id (#PCDATA)>
<!ELEMENT name (#PCDATA)>
<!ELEMENT email (#PCDATA)>
```

> [!tip] connect DTD to XML file

```xml
<!-- iti.xml -->
<!-- well formed but not valid -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE students SYSTEM "c:\iti.dtd">
<students>
    <student>
        <id>1</id>
        <name>John</name>
        <email>
        as@asd.as
        </email>
    </student>
    <student>
        <id>2</id>
        <name>Smith</name>
        <email>
        as@asd.as
        </email>
        <email>
        as@asd.as
        </email>
    </student>
    <student>
        <id>2</id>
        <name>Smith</name>
    </student>
</students>
```

> [!done] solve errors by editing DTD

```dtd
<?xml version="1.0" encoding="UTF-8"?>
<!ELEMENT students ( student+ )>
<!-- multiple students -->
<!ELEMENT student ( id,name,email* )>
<!-- zero or more emails -->
```

> [!bug] `,` and keep sequence
>
> - cannot have email before id

> [!example] attributes
>
> - adding attributes to tags without adding them to DTD will throw error.
> - once we connect DTD to XML file we have to declare all tags and attributes in DTD.
> - `<!ATTLIST tagName attributeName attributeType exist(#REQUIRED) | #IMPLIED>`

> [!tip]
>
> 1. CDATA:
>
> - character data
> - allow any character inside value of the attribute (disallowed characters: `&` `<` `>` `'` `"`)
> - "i$i % mansoura"
>
> 1. NMTOKEN:
>
> - name token
> - allow only letters, digits, `_`, `.`, `-` =>"iti"
>
> 3.  NMTOKENS:
>
> - name tokens
> - allow only letters, digits, `_`, `.`, `-`, and space => "20 years old"
>
> 4.  ENUMERATION:
>
> - like enum in C#
>
> - only allow the values specified in the attribute declaration.
> - `<!ATTLIST tagName attributeName (value1 | value2 | value3) >`

```xml
<!-- iti.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE students SYSTEM "c:\iti.dtd">
<students branch="mansoura">
    <student track="pd">
        <id>1</id>
        <name>John</name>
        <email>
```

```dtd
<?xml version="1.0" encoding="UTF-8"?>
<!ELEMENT students ( student+ )>
<!ELEMENT student ( id,name,email* )>
<!ELEMENT id (#PCDATA)>
<!ELEMENT name (#PCDATA)>
<!ELEMENT email (#PCDATA)>
<!ATTLIST students branch CDATA #REQUIRED>
<!-- CDATA: character data can write anything -->
<!ATTList students branch NMTOKEN #REQUIRED>
<!-- NMTOKEN: name token -->
<!ATTList students branch NMTOKENS #REQUIRED>
<!-- NMTOKENS: name tokens -->


<!ATTLIST student track CDATA #IMPLIED>
<!-- #IMPLIED: optional -->

<!ATTLIST student track (pd | os| ai) >
<!-- (pd | os| ai): enumeration default required-->
<!-- enumeration: only allow the values specified in the attribute declaration. -->
<!ATTLIST student track (pd | os| ai) "pd" >
<!-- "pd": default value if not specified -->


<!ATTLIST student grade CDATA #IMPLIED>

<!-- we can list all the attributes in one line -->

<!ATTLIST student track (pd | os| ai) "pd" grade CDATA #IMPLIED>
```

> [!tip] to display data styled
>
> - we need to put it in ==tags== not attributes.

```css
students {
  display: block;
  border: 1px solid black;
  width: 200px;
  margin: 10px auto;
  padding: 10px;
  text-align: center;
}
student {
  display: block;
  border: 1px solid black;
  width: 200px;
  margin: 10px auto;
  padding: 10px;
  text-align: center;
}
id,
name,
email {
  display: block;
  border: 1px solid black;
  width: 200px;
  margin: 10px auto;
  padding: 10px;
  text-align: center;
}
```

> [!tip] link css to xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/css" href="style.css"?>
```

> [!warning] DO NOT USE mixed content elements
>
> - not recommended
```xml
 <students>
 iti students
 <student>
 </student>
 </students>
```

### #lab1
