# splitMulticlassFile
Split a PHP file containing many class definitions into many files, one file per class definition.

### Definition of a multiclass file
A multiclass file contains many class definitions in one source file.

### Context of the multiclass file
- PHP application in modern MVC framework with <a href="http://php.net/manual/en/language.oop5.autoload.php">autoloader</a> expecting one PHP source file per class definition
- API to an external service with a PHP “toolkit”
- API of external service has PHP toolkit with a multiclass file: i.e., hundreds of class definitions in a single source file

### Advantage of having all the classes in the multiclass file available to the IDE
An IDE such as PhpStorm, NetBeans, or Eclipse inspects class definitions and provides helpful features such as code autocomplete, inline documentation, and validation. Such features accelerate software development and minimize errors. With an IDE, the classes in the multiclass file become a dynamic reference of properties, data types, and commented documentation.

### Disadvantage of the multiclass file at runtime
The multiclass file is inefficient at runtime:
- The multiclass file is very large and contains hundreds of class definitions.
- The caller rarely needs more than a few class definitions in a single process.
- Including the multiclass file instantiates all classes, thereby consuming far more memory and processing than what is necessary for the caller’s needs.

Take, for example, the <a href="http://www.netsuite.com/portal/developers/resources/suitetalk-documentation.shtml">NetSuite SuiteTalk SOAP API</a> <a href="http://www.netsuite.com/portal/developers/resources/suitetalk-sample-applications.shtml">PHP Toolkit</a>: the file <a href="http://www.netsuite.com/download/PHPToolkit_2016_1.zip">NetSuiteService.php</a> contains over 1,500 class definitions and exceeds 2.6 MB. By contrast, most SuiteTalk API transactions use just a few objects from the WSDL. Typical SuiteTalk integrations can easily fire off thousands of API transactions per hour. If the underlying PHP process includes a 2.6 MB file and instantiates over 1,500 classes, then the burden on server resources would be unnecessarily costly and, indeed, wasteful.

### Solution strategy: split the multiclass file into many files
Divide the multiclass file into many files, one file per class definition. The IDE inspects all classes, while the MVC autoloader loads classes only as necessary.

### Strategy or technical plan for “split” script
- Generally useful—it can handle any multiclass file most of the time
- Optional <a href="http://php.net/manual/en/language.namespaces.definition.php">namespace</a> declaration
- Handle class declarations wrapped in a <a href="http://php.net/manual/en/function.class-exists.php">class_exists()</a> “check”
- Use <a href="http://php.net/manual/en/function.require.php">require()</a> to include multiclass file and instantiate classes
- Use <a href="http://php.net/manual/en/function.get-declared-classes.php">get_declared_classes()</a> to get the class names
- Use reflection to identify the multiclass classes from among the declared classes and get the start and end lines from the source file
- Use standard <a href="http://php.net/manual/en/ref.filesystem.php">PHP filesystem functions</a> to get the multiclass source code, parse it, and write single-class files.

### How to use splitMulticlassFile.php
