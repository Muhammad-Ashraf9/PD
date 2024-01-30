### XML Schema alternative for DTD

> [!tip] XML Schema vs DTD
> DTD has its own syntax.
>
> - XML Schema is a more powerful alternative to DTD
> - can't use types like integer, date, etc. in DTD

> [!example] XML Schema (.xsd)
>
> - input: XML Schema (.xsd) + XML document
> - Parser: Validator
> - Output: Schema Valid / Invalid

> [!tip] XML Schema (.xsd)
>
> - same rules but different syntax
> - start with `<schema>` and end with `</schema>` root element

> [!tip] Type system for XML Schema
>
> - Integer: -32768 to 32767 (schema integer:accepts negative values)
> - Decimal.
> - Date
>   > [!warning] type system for user defined types is different from XML Schema type system
>   >
>   > - we can set integer type for to accept only positive values between 0 to 100
>   > - naming collision.
>   > - in C# we can't create a class with same name in same namespace (we use namespace to avoid naming collision)
>   >   > [!done] namespace `xs`
>   >   >
>   >   > - XML Schema has its own namespace for user defined types
>   >   > - to avoid naming collision in XML Schema we use namespace `xs`
>   >   > - `<xs:schema></xs:schema>`
>   >   > - we use `xs` for directives `<xs:element></xs:element>` `<xs:attribute>`

> [!tip] user type vs schema type
>
> - define user type in XML Schema
> - `<xs:element name="age" type="integer"/>`
> - schema type: `xs:integer`
> - `<xs:element name="age" type="xs:integer"/>`

> [!example] Types of Elements
>
> 1. pairs or couple tags `<name>John</name>` (simple element)
> 2. Mono: self closing | empty tag `<person name="John"/>` (complex element: has attributes but no child elements)
> 3. tree: nested elements `<person name="John"> <age>20</age> </person>`` (complex element: has attributes and child elements)

> [!done] Complex element
>
> - has attributes or child elements

> [!example] `all` vs `choice` vs `sequence`
>
> - `sequence` => in order
> - `all` => unordered
> - `choice` => one of them

> [!warning] minOccurs vs maxOccurs
>
> - minOccurs: minimum number of occurrences
> - maxOccurs: maximum number of occurrences

```xml
    <person>
        <id>1</id>
        <name>John</name>
        <city>London</city>
    </person>
```

> [!tip] `.xsd` file

```xsd
    <xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified" attributeFormDefault="unqualified">
    <!-- qualified => element must be qualified with namespace but attribute must not be qualified with namespace -->
      <!-- <xs:element name="person" /> -->
        <xs:element name="person" >
        <!-- complex type we can use complex type and write why it is complex
         -->
            <xs:complexType>
                <xs:sequence>
                    <!-- <xs:element name="id" type="integer" /> -->
                    <!-- error as we didn't define integer type -->
                    <xs:element name="id" type="xs:integer" />
                    <xs:element name="name" type="xs:string" />
                    <xs:element name="city" type="xs:string" />
                    <xs:element name="email" type="xs:string" maxOccurs="2" />
                    <!-- to have at most 2 emails -->
                    <xs:element name="email" type="xs:string" minOccurs="0" maxOccurs="2" />
                    <!-- to have at most 2 emails and at least 0 emails -->
                </xs:sequence>
            </xs:complexType>


        </xs:element>


    </xs:schema>
```

> [!example] we can create our own schema

```xsd
<!--
    to have sequence of persons
 -->
<xs:element name="persons">
    <xs:complexType>
        <xs:sequence>
            <xs:element name="person" maxOccurs="unbounded">
            <!--
                default is maxOccurs="1" minOccurs="1"
                maxOccurs="unbounded" => unlimited
             -->
                <xs:complexType>
                    <xs:sequence>
                        <xs:element name="id" type="xs:integer" />
                        <!--
                            we can use negative values in schema integer
                            to restrict big range to small range (restrictions)
                            to expand small range to big range (extensions)
                         -->
                        <xs:element name="name" type="xs:string" />
                        <xs:element name="city" type="xs:string" />
                        <xs:element name="email" type="xs:string" maxOccurs="2" />
                    </xs:sequence>
                </xs:complexType>
            </xs:element>
        </xs:sequence>
    </xs:complexType>

```

> [!example] to decalre user defined simple type
>
> - declaration in XML Schema scope

```xsd
<xs:simpleType name="integer">
    <xs:restriction base="xs:integer">
    <!-- facets -->
        <xs:minInclusive value="0" />
        <xs:maxInclusive value="100" />
        <!-- there is minExclusive and maxExclusive as well -->
    </xs:restriction>
</xs:simpleType>
    <!-- we use new integer type in our schema
    <xs:element name="age" type="integer" />
     -->
<!-- integer will throw error if it is not between 0 and 100 -->
```

> [!warning] we can define our own type locally
>
> - decalre it in element scope (local scope) we can't use it outside of element scope
>   > [!done] better to define it in schema scope (global scope) we can use it anywhere in schema
>   >
>   > - previous example

```xsd
<xs:element name="id">
    <xs:simpleType>
        <xs:restriction base="xs:integer">
        <!-- Facets: AND -->
            <xs:minInclusive value="0" />
            <xs:maxInclusive value="100" />
        </xs:restriction>
    </xs:simpleType>
</xs:element>
```

> [!bug] self closing tag is ==valid==

> [!tip] decalre city type to be one of these values
> Simple Type: restrict string (enumeration)
>
> - mansoura
> - cairo
> - alexandria

```xsd
<xs:simpleType name="cityType">
    <xs:restriction base="xs:string">
    <!-- facets : OR -->
        <xs:enumeration value="mansoura" />
        <xs:enumeration value="cairo" />
        <xs:enumeration value="alexandria" />
    </xs:restriction>
</xs:simpleType>
```

> [!tip] Decalre Address Type (pattern)

```xsd
    <xs:simpleType name="addressType">
        <xs:restriction base="xs:string">
            <!-- 3digits-4CapitalLetters-3digits -->
            <xs:pattern value="\d{3}-[A-Z]{4}-\d{3}" />
            <!-- we can add more patterns -->
            <xs:pattern value="\d{2}-[A-Z]{3}-\d{2}" />
        </xs:restriction>
    </xs:simpleType>

```

> [!danger] Declare attribute for persons
>
> - it is one of the reasons person is complex element(after sequence)
> - WE HAVE TO DECLARE IT AFTER SEQUENCE
>   > [!done] we can't use `maxOccurs` with attributes as it is a rule that we can't have more than one attribute with same name

```xsd
<xs:attribute name="gender" type="xs:string" />
<!-- restrict on some values like null -->
<xs:attribute name="gender" type="xs:string" use="required" />

<!-- restrict on 2 values male , female -->
<xs:attribute name="gender">
    <xs:simpleType>
        <xs:restriction base="xs:string">
            <xs:enumeration value="male" />
            <xs:enumeration value="female" />
        </xs:restriction>
    </xs:simpleType>
    </xs:attribute>

```

> [!tip] we can define another type on other user defined type

---
### BREAK
---
>[!tip] Bookstore Schema

```xsd
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified" attributeFormDefault="unqualified">
<!-- publishertype => xs:string -->
    <xs:simpleType name="publisherType">
        <xs:restriction base="xs:string">
            <xs:enumeration value="O'Reilly" />
            <xs:enumeration value="Wrox" />
            <xs:enumeration value="Apress" />
        </xs:restriction>
    
    <xs:element name="bookstore">
        <xs:complexType>
            <xs:sequence>
                <xs:element name="book" maxOccurs="unbounded">
                    <xs:complexType>
                        <xs:sequence>
                            <xs:element name="title" type="xs:string" />
                            <xs:element name="author" type="xs:string" />
                            <xs:element name="year" type="xs:integer" />
                            <xs:element name="price" type="xs:decimal" />
                        </xs:sequence>
                        <xs:attribute name="category" type="xs:string" />
                    </xs:complexType>
                </xs:element>
            </xs:sequence>
        </xs:complexType>
    </xs:element>
```

```xml
    <bookstore>
        <book track="1" branch="mansoura">
            <title>XML</title>
            <author>John</author>
            <date>2010</date>
            <isbn>100</isbn>
            <publisher>O'Reilly</publisher>
        </book>
        <novel>
            <title>XML</title>
            <author>John</author>
            <date>2010</date>
            <isbn>100</isbn>
            <publisher>O'Reilly</publisher>
        </novel>
    </bookstore> 
```
>[!warning] Make Bookstrore self closing tag




>[!tip] repaeatable elements
> - tightly coupled => loosly coupled
> - we define it in global scope
> - we already defined it so we refer to it (ref)


>[!tip] group

```xsd
<xs:group name="bookGroup">
    <xs:sequence>
        <xs:element name="title" type="xs:string" />
        <xs:element name="author" type="xs:string" />
        <xs:element name="year" type="xs:integer" />
        <xs:element name="price" type="xs:decimal" />
    </xs:sequence>
</xs:group>
```
>[!tip] we can use refernce and define it in global scope

```xsd
    <xs:element name="title" type="xs:string" />
    <xs:element name="author" type="xs:string" />
    <xs:element name="year" type="xs:integer" />
    <xs:element name="price" type="xs:decimal" />

    <!-- group
     -->
    <xs:group name="bookGroup">
        <xs:sequence>
            <xs:element ref="title" />
            <xs:element ref="author" />
            <xs:element ref="year" />
            <xs:element ref="price" />
        </xs:sequence>
    </xs:group>

    <xs:element name="book">
        <xs:complexType>
            <xs:sequence>
                <xs:group ref="bookGroup" />
            </xs:sequence>
            <xs:attribute name="category" type="xs:string" />
        </xs:complexType>
    </xs:element>
    <!-- novel
     -->
    <xs:element name="novel">
        <xs:complexType>
            <xs:sequence>
                <xs:group ref="bookGroup" />
            </xs:sequence>
            <xs:attribute name="category" type="xs:string" />
        </xs:complexType>
    </xs:element>
```

>[!danger] `use="required"` vs `use="optional"`
> - can't use `use="required"` `use="optional"` with global defined elements.
> - but can use it with local defined elements.

>[!tip] we can have attribute group
    
```xsd
    <xs:attributeGroup name="bookAttributeGroup">
        <xs:attribute name="category" type="xs:string" />
        <xs:attribute name="track" type="xs:integer" />
        <xs:attribute name="branch" type="xs:string" />
    </xs:attributeGroup>
```

>[!done] use attribute group

```xsd
    <xs:element name="book">
        <xs:complexType>
            <xs:sequence>
                <xs:group ref="bookGroup" />
            </xs:sequence>
            <xs:attributeGroup ref="bookAttributeGroup" />
        </xs:complexType>
    </xs:element>
```

