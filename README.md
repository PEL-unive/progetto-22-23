# Project: Programming and Laboratory A.A. 2022/2023

The goal of the project is to write a parser (using context-free grammars) for files in JSON format. The content of the file must be saved in a C++ container that allows access (via iterators and operator overloading) to the content read from the JSON file.

The following two sections introduce the JSON format and the description of the container to be created.

**Note**: If you find inaccuracies/errors or need clarification on parts of this document or json.hpp, open a GitHub issue (click the "issues" button at the top) and reference the line number of the relevant file (if the comment pertains to a specific line of the code or this README). To reference a particular line of README.md, open the file (by clicking on README.md at the top), make the line numbers visible (click the <> "display the source blob" button), select the relevant line numbers -> click the three dots -> "copy permalink." You can then paste the copied link in the issue. The same process applies for json.hpp (in this case, the line numbers are immediately visible).

## 1. JSON Format

We want to write a parser for a _simplified_ variant of JSON – a textual format for data exchange on the Web. **Important**: Use the JSON description provided below, not the one found online.

A JSON file contains data in a tree structure (recursive), similar to the XML format. Unlike XML, the JSON format allows both **lists** of values and **dictionaries** (defined in detail below). Overall, a JSON file can be a list, a dictionary, a number (double), a boolean, a string, or null.

### Lists

A list is a sequence of heterogeneous values separated by commas and enclosed in square brackets. The values in the list can be of **six** types: a list (recursion), a dictionary (defined below), a string enclosed in double quotes, a double value, a bool (true/false), or null (indicating the absence of a value).

The following is an example of a simple JSON file of type list (i.e., the entire file is a list), containing two strings, a double value (34.5), followed by a bool value (true), followed by null, followed by a list.

```json
[
    "first string",
    "second string",
    34.5,
    true,
    null, 
    [1, 2, false, 43]
]
```

**Note.** Separators (newlines, tabs, spaces) should be ignored. The only exception is separators enclosed in double quotes (e.g., the space in "first string"); in this case, the separators must be part of the string extracted from the file.

### An Important Note on Strings

All characters between double quotes should be extracted as they are. Keep in mind that strings may contain the `"` character preceded by a backslash (i.e., the string might contain the substring `\"`). In this case, the string **does not** end at that occurrence of `"`. Strings only end when the character `"` is **not** preceded by a backslash (`\`).

**Note**: Be careful to follow this rule!

Below are some examples.

For example, consider the following JSON file:

```json
[
    "a string with \n quotes \"escaped\"",
    "second string without escape",
    "third string with more\" escapes",
    "double \\ escape"
]
```

In the above example, the four strings to be extracted from the file (i.e., the characters to be inserted into the std::string you will construct) are as follows:

	a string with \n quotes \"escaped\"
	second string without escape
	third string with more\" escapes
	double \\ escape

**Note**: Special characters like `\n` (and similar, such as `\t`, `\r`, etc.) are no exception! If the JSON file contains a string with `\n`, for example, you should extract **two** characters: a backslash (`\`) followed by the character `n`, **not** the single character `\n` (which means newline). This is a modification from the original format that we've introduced to simplify writing the parser.

### Dictionaries

A dictionary is a set (a list) of key-value pairs

**key : value**

separated by commas and enclosed in curly braces. Note that the key and value are separated by a colon (‘:’). Keys can only be strings enclosed in double quotes (again, these strings can contain escape characters ‘\\’). The values can be, as mentioned above, a list, a dictionary, a string enclosed in double quotes (possibly with escape characters ‘\\’), a double value, a bool, or null.

For example, the following JSON file is of type dictionary.

```json
{
    "first key": 5,
    "second key": [4.12, 2, true],
    "third key": "a string",
    "fourth key": {"a": 4, "b": [4, 5]}
}
```

In the example above, the dictionary contains four key-value pairs:

1. In the first pair, the key is the string **first key** and the value is the integer **5**.
2. In the second pair, the key is the string **second key** and the value is the list **[4.12, 2, true]**.
3. In the third pair, the key is the string **third key** and the value is the string **a string**.
4. In the fourth pair, the key is the string **fourth key** and the value is again (recursively) a dictionary: **{"a": 4, "b": [4, 5]}**.

Note the recursive structure of the JSON format: your grammar must allow arbitrary nesting of dictionaries and lists.

### Examples and Datasets to Test the Parser

You can use the following JSON file database to test your code:

<https://github.com/jdorfman/awesome-json-datasets>.

It might be useful to view a JSON file in tree format. For this, you can use one of the many online JSON viewers, such as the following:

<https://jsonviewer.stack.hu/>.

**Note.** The viewer above encloses strings in double quotes. Your parser, however, should not extract the double quotes that enclose the strings (see examples above).





## 2. Container

The goal of the project is to write a container

```cpp
class json;
```

that allows storing all the information contained in a JSON file. The JSON parser implemented will be used to extract data from a JSON file and construct an object of type `json`. Below is a description of the declaration of the `json.hpp` class that you will find in this GitHub repository.

The `json` class should be defined recursively, as follows. An object of type `json` contains a single private variable

```cpp
impl* pimpl;
```

Where `json::impl` is a (private) struct that you are free to define (as seen in Module 1: Pimpl pattern). When deciding which variables and methods to define inside `json::impl`, keep in mind that each `json` instance can be one of the following six types:

1. null,
2. a number,
3. a boolean,
4. a string,
5. a list (of json), or
6. a dictionary (a set of <string, json> pairs).

A good idea might be to define inside `json::impl` a `double` variable, a `bool` variable, a `std::string` variable, a list of `json`, and a list of `std::pair<std::string, json>` for the dictionary (in addition to methods and other variables you deem necessary). Each `json` object will use only one of these five variables (similarly to how we handled the `token` class in the lesson), and you could model the null value by checking that none of these five variables are “in use.”

### Exceptions

In the `json.hpp` file, the struct

```cpp
struct json_exception {
    std::string msg;
};
```

is defined, and you should use it for exceptions. Use the `msg` string to provide an informative error message (it will be useful for debugging: in our reports, we will print these messages).

### Member Types of `json`

```cpp
json::impl;
```

This is the (private in `json`) struct that you can use to define the variables and methods you want to use in the implementation of `json` (as mentioned earlier, a `json` object contains a pointer `impl*`).

Remember that `json::impl` has access to the private members of `json`, so if desired, you can also define methods inside `json::impl` that take `json` objects as input and modify their content (accessing private members of `json`).

---

```cpp
json::list_iterator;
json::dictionary_iterator;
json::const_list_iterator;
json::const_dictionary_iterator;
```

These are forward iterators that allow navigation through lists and dictionaries contained in a `json` object (more details are described below in the methods `begin_list()`, `end_list()`, `begin_dictionary()`, `end_dictionary()`). For each of these iterators, define (as seen in class) the methods for modifying the iterators and accessing their content (in particular: `operator++` prefix and postfix, `operator*`, `operator->`, `operator==`, `operator!=`).

### Public Methods of `json`

#### Constructors, Destructor, and Assignment Operators:

Default constructor, copy constructor, move constructor, destructor, copy assignment, and move assignment, implemented with the standard semantics as seen in class. The default constructor should create a `json` object of type null (i.e., the function `is_null()` described below should return `true`).

```cpp
json::json();
json::json(json const&);
json::json(json&&);
json::~json();
json& json::operator=(json const&);
json& json::operator=(json&&);
```

#### Boolean Methods

The following methods can be used to determine the type of the `json` object (list, dictionary, string, number, boolean, or null). Note: exactly **one** of these methods should return true (the `json` object is exactly one of these six types).

---

```cpp
bool json::is_list() const;
```

This method returns true if and only if the object is a list.

---

```cpp
bool json::is_dictionary() const;
```

This method returns true if and only if the object is a dictionary.

---

```cpp
bool json::is_string() const;
```

This method returns true if and only if the object is a string.

---

```cpp
bool json::is_number() const;
```

This method returns true if and only if the object is a number (double).

---

```cpp
bool json::is_bool() const;
```

This method returns true if and only if the object is a boolean.

---

```cpp
bool json::is_null() const;
```

This method returns true if and only if the object is null.

#### Methods for Accessing the Content of the Container

```cpp
json const& json::operator[](std::string const& key) const;
json& json::operator[](std::string const& key);
```

If the object is a dictionary (`is_dictionary()` returns `true`), this method returns a reference to the `json` element associated with the key `key` (remember that a dictionary is a set of key-value pairs where keys are of type `std::string` and values are of type `json`).

**Note:** It is not a problem if this method scans the entire content of the dictionary (for example, if you implemented the dictionary as a list). We do not expect this method to be particularly efficient (unlike the `json::insert` method, see below).

If the dictionary does not contain any key-value pair where the key is equal to `key`, then:

- The `operator[]` method must insert a new key-value pair into the dictionary with the key as `key` and the value as a `json` constructed with the default constructor (i.e., a `json` whose content is `null`), and finally return a reference to this newly constructed `json`;
- The `operator[] const` method must throw an exception since no insertion can be performed.

In any case, if the object is not a dictionary, a `json_exception` exception should be thrown (with the message `msg` of your choice) when the `operator[]` method is invoked.

---

If the object is a list (`is_list()` returns `true`), use the `list_iterator` and `const_list_iterator` iterators to access the members of the list (which are themselves `json` objects).

The following methods allow you to obtain iterators at the beginning and end of the list. If `is_list()` returns `false`, these methods must throw a `json_exception` exception (with the message `msg` of your choice).

**Warning:** The order of elements in the list must be the same as the file read in input. In particular, the iterators must return the elements in this order.

```cpp
json::list_iterator json::begin_list();
json::const_list_iterator json::begin_list() const;
json::list_iterator json::end_list();
json::const_list_iterator json::end_list() const;
```

---

Similarly, if the object is a dictionary (`is_dictionary()` returns `true`), use the `dictionary_iterator` and `const_dictionary_iterator` iterators to access the pairs contained in the dictionary (the pairs are of type `std::pair<std::string, json>`). The following methods allow you to obtain iterators at the beginning and end of the dictionary. If `is_dictionary()` returns `false`, these methods must throw a `json_exception` exception (with the message `msg` of your choice).

**Warning:** Unlike the list iterators, the order in which dictionary elements are stored and accessed is not important.

```cpp
json::dictionary_iterator json::begin_dictionary();
json::const_dictionary_iterator json::begin_dictionary() const;
json::dictionary_iterator json::end_dictionary();
json::const_dictionary_iterator json::end_dictionary() const;
```

---

If `is_number()` is `true` (the object is a number), the methods below return a reference/const reference to the `double` variable contained in the `json` (which you can store in `json::impl`). If `is_number()` is `false`, this method must throw a `json_exception` exception (with the message `msg` of your choice).

```cpp
double& json::get_number();
double const& json::get_number() const;
```

---

If `is_bool()` is `true` (the object is a boolean), these methods return a reference/const reference to the boolean variable contained in the `json` (which you can store in `json::impl`). Otherwise, a `json_exception` exception should be thrown (with the message `msg` of your choice).

```cpp
bool& json::get_bool();
bool const& json::get_bool() const;
```

---

If `is_string()` is `true` (the object is a string), these methods return a reference/const reference to the string variable contained in the `json` (which you can store in `json::impl`). Otherwise, a `json_exception` exception should be thrown (with the message `msg` of your choice).

```cpp
std::string& json::get_string();
std::string const& json::get_string() const;
```

#### Methods to Set the Content of the `json`

This method makes the `json` of type string, setting its internally stored string equal to `x` and deleting any data previously stored in the `json` (for example, if the `json` was of type list, this function must empty the list and then store the string `x`). After calling this function, `is_string()` should return `true` (no other boolean method should return `true`).

```cpp
void json::set_string(std::string const& x);
```

---

This method makes the `json` of type boolean, setting the internally stored boolean to `x` and deleting any data previously stored in the `json` (for example, if the `json` was of type list, this function must empty the list and then store the boolean `x`). After calling this function, `is_bool()` should return `true` (no other boolean method should return `true`).

```cpp
void json::set_bool(bool x);
```

---

This method makes the `json` of type number, setting the internally stored `double` to `x` and deleting any data previously stored in the `json` (for example, if the `json` was of type list, this function must empty the list and then store the `double` `x`). After calling this function, `is_number()` should return `true` (no other boolean method should return `true`).

```cpp
void json::set_number(double x);
```

---

This method makes the `json` of type null, deleting any data previously stored in the `json` (for example, if the `json` was of type list, this function must empty the list and then store the fact that the object is a null `json`). After calling this function, `is_null()` should return `true` (no other boolean method should return `true`).

```cpp
void json::set_null();
```

---

This method makes the `json` of

 type list, deleting any data previously stored in the `json` (for example, if the `json` was of type string, this function must empty the string and then store the list). After calling this function, `is_list()` should return `true` (no other boolean method should return `true`).

```cpp
void json::set_list();
```

---

This method makes the `json` of type dictionary, deleting any data previously stored in the `json` (for example, if the `json` was of type string, this function must empty the string and then store the dictionary). After calling this function, `is_dictionary()` should return `true` (no other boolean method should return `true`).

```cpp
void json::set_dictionary();
```


---


If the `json` is a list type (i.e., `is_list()` returns true), this method adds `x` to the front of the list.

**Important:** For efficiency reasons, the method should not traverse the entire list! Implement the method as seen in class.

If the `json` is not a list type, this function must throw a `json_exception` (with a custom message `msg`).

```cpp
void json::push_front(json const& x);
```

---


If the `json` is a list type (i.e., `is_list()` returns true), this method adds `x` to the end of the list.

**Important:** For efficiency reasons, the method should not traverse the entire list! Implement the method as seen in class.

If the `json` is not a list type, this function must throw a `json_exception` (with a custom message `msg`).

```cpp
void json::push_back(json const& x);
```

---



If the `json` is a dictionary type (i.e., `is_dictionary()` returns true), this method adds the key-value pair `x` to the dictionary. The method does not need to check if a pair with the key `x.first` already exists in the dictionary (you can assume this will never happen in the tests we run).

**Important:** For efficiency reasons, the method should not traverse the entire dictionary!

If the `json` is not a dictionary type, this function must throw a `json_exception` (with a custom message `msg`).

```cpp
void json::insert(std::pair<std::string, json> const& x);
```

---

### External Methods (Non-member of `json`)



```cpp
std::ostream& operator<<(std::ostream& lhs, json const& rhs);
```

This method writes the `rhs` object to the output stream `lhs` in valid JSON format (see the JSON format description at the beginning of the document). Note: separators (spaces, tabs, newlines) are optional in the JSON format (you can choose whether or not to include them: it is equivalent).

---



```cpp
std::istream& operator>>(std::istream& lhs, json& rhs);
```

This method reads a `json` object from `lhs` and stores it in `rhs` (overwriting its content). This is the function that triggers the JSON parser: the input stream is a character stream that contains a JSON document (as described at the beginning). The `>>` operator must extract the data from the stream (using the parser) and construct the `json` object containing those data (erasing the previous content of `rhs` if it exists).

If the function encounters parsing issues, it must throw a `json_exception` (with a custom message `msg`). Try to detect as many parsing errors as possible (we will test your code with incorrectly formatted JSON files and check that the exception is thrown).

---

### Parser Suggestions

We suggest writing the parser as follows (as seen in class):

1. Define a context-free grammar that can recognize the JSON format, following the description provided at the beginning of the document.
2. Create a function for each non-terminal symbol in the grammar.
3. Each function created in step 2 should receive a reference to `std::istream` as input and return a `json` object as output. For example, if you write a `LIST` function that parses an entire list from the input stream, this function will construct a `json` object based on that list (of `json` objects) and return it. This will allow you to implement functions recursively: the `LIST` function, in turn, will call other functions to extract the components of the list, which will return `json` objects. These objects will then be inserted into the list of the returned `json` object.

---

## 3. Final Goal and Example of How the Code Will Be Tested

The `json` container created can be used as a JSON file editor. Once loaded, the file can be manipulated/navigated using the defined operators. For example, suppose `std::cin` contains the stream:

```json
[
    1,
    {
        "first key": 5,
        "second key": [4.12, 2, true],
        "third key": "a string",
        "fourth key": {"a": 4, "b": [4, 5]}
    },
    3
]
```

Then, the following code is valid and should print "4":

```cpp
json j;
std::cin >> j;
json& y = *(++j.begin_list());
std::cout << y["fourth key"]["a"];
```

Additionally, the class allows us to modify a `json` object. For example, if the variable

```cpp
json z;
```

contains the following data:

```json
{"c": 5, "d": 6}
```

and `j` is the `json` object defined earlier, then after the assignment

```cpp
(*(++j.begin_list()))["first key"] = z;
```

The variable `j` should contain the following data:

```json
[
    1,
    {
        "first key": {"c": 5, "d": 6},
        "second key": [4.12, 2, true],
        "third key": "a string",
        "fourth key": {"a": 4, "b": [4, 5]}
    },
    3
]
```

---

## 4. How to Test Your Code

Remember the general rule: writing the code is only half the job! The other half is designing thorough tests to catch any bugs. During evaluation, our task will be to stress-test your code (testing it with various JSON files, both valid and invalid, and combining operators in all possible ways), so design good tests and remember to compile the code with debugging tools shown in class (asserts, compiler sanitizers). Also, remember to use Valgrind.

That said, we recommend creating a `test.cpp` file where you define a `main` function to test your `json` object. This `test.cpp` file **should not** be submitted (you will only submit `json.cpp`, see below). It is only for you to test your code. Test each `json` method in isolation with many test cases. Ensure there are no memory leaks. The Valgrind output should always end with:

```text
ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

Any memory errors found will result in penalties.

---

## 5. Project Submission

We provide you with a `json.hpp` file (in this GitHub repository) that contains the class declarations and the only allowed `#include`s.

**Note:** The `json.hpp` file **must not** be modified in any way. We will compile and test your code with the `json.hpp` file provided by us (not the version you might have modified).

Your task will be to implement the `json.cpp` file, which contains the definition of all the methods/types described above. Important notes:

1. `json.cpp` can only include `json.hpp`. It cannot include any other `#include` statements or macros (in general, no code preceded by `#`, except `#include "json.hpp"`). All includes and macros will be automatically removed before compiling your code. The only allowed `#include`s are those in the `json.hpp` file provided by us.
2. `json.cpp` **must not** define the `main` function: we will write it to test your code. If you define `main`, the code will not compile.

---

### Format and Submission Link

Each student must submit their `json.cpp` file (exactly with this name), inside a folder named after their student ID, compressed into a `.tar.gz` archive as described below. This archive is the only file to be submitted via the Moodle module found in the course page: <https://moodle.unive.it/course/view.php?id=13453>.

Example: if my student ID is 012345 and I have a folder containing my `json.cpp` implementation, to create the archive, I should follow these steps (from the Ubuntu terminal):

```bash
mkdir 012345
mv json.cpp 012345
tar -zcvf 012345.tar.gz 012345
```

These steps create the `012345.tar.gz` file, which should be submitted via Moodle. To verify that the archive was created correctly, use the following command to extract it:

```bash
tar -xvzf 012345.tar.gz
```

**Important:** Follow **exactly** these instructions. We will run a script that automatically extracts and compiles your projects, so any minor mistake (e.g., incorrect archive format, wrong folder name, or incorrect file name) will result in your project being automatically excluded.

The deadlines for June are:

- **June 14, 23:59**
- **June 27, 23:59**

The next appeals will be in September and January.

The Moodle submission module will be opened soon, and you can start submitting right away. You can resubmit an unlimited number of times. At regular intervals (usually every 2 weeks), we will download all the submitted projects and test them, reporting any issues (e.g., memory leaks, segmentation faults) directly to the concerned students. After the deadline, the submission module will be disabled, and the last submission you made will be evaluated.

**Important:** We recommend submitting well in advance of the deadline to receive useful feedback! As mentioned above, we test your code approximately every 2 weeks. Avoid submitting at the last minute: after submission, you cannot make changes, so if your program contains bugs, you will be evaluated negatively.

---

## 6. Project Evaluation

Each method you write will be thoroughly tested by us on many different inputs (both valid and invalid JSON files).

We will compile your code using the C++17 standard (compiler flag `-std=c++17`).

A method that causes an unexpected interruption (e.g., segmentation fault) will receive 0 points. A method that is not implemented will receive 0 points. Some methods, such as those on iterators, are crucial because they allow us to test your code (by accessing the contents of the container): be careful to implement them correctly, or we will have no way to test your code.

### Timeout

Attention: your code must be reasonably fast. We will set a timeout of a few minutes (it should actually take only a few seconds) for reading JSON files and constructing the corresponding `json` object (see the provided JSON dataset). If your code takes too long to parse a large JSON file, you likely implemented data insertion into lists/dictionaries incorrectly: remember that insertions should not traverse all elements in the list/dictionary (they should only allocate a new memory cell and adjust a few pointers, if you use lists for both structures).

### Plagiarism

Your code will be compared using a plagiarism detector. This tool can detect attempts to mask copied code by renaming variables, converting `for` loops into `while` loops, etc. Any projects involved in copying will be automatically considered failed. Keep these simple rules in mind:

1. **Never** share your code with a classmate who still needs to take the exam. They might copy it (even partially), and both of you will fail. Discussing general ideas about the solution is fine, but the code must never be shared.
2. Do not post parts of your code online (e.g., on forums). Other students might copy it, and both of you will fail the exam (this has happened before).



