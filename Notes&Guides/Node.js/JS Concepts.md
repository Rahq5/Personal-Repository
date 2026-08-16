In this note i will show a summarized explanation on JS concept and all past knowledge for me will be skipped

# **JavaScriptDefining**

JavaScript is a **programming language** used to make dynamic content on websites. It is:

- **Lightweight**, **cross-platform** ("code once – write everywhere").
- **Single-threaded**, **OOP (Object-Oriented Programming)**.
- **Non-blocking**, **event-driven**.
- Used by major companies like **Google** and **Facebook**, making it highly relevant for learning.
## Two Types of JavaScript Execution

### 1. Client-Side JavaScript

- Part of the **web trio** (**HTML, CSS, JavaScript**).
- Enables **functionality**, **user interaction**, and brings web pages to life.
- Runs **directly in the user's browser** (not on the server).
### 2. Server-Side JavaScript (Node.js)

- Used on **web servers** for:
    - **Database access**
    - **File handling**
    - **Security features**
    - **Sending responses** to browsers.

## The Journey of a `.js` File (Node.js Execution Flow)

### 1. **Loading & Memory Allocation**
- The code is loaded and stored in memory.

### 2. **Parsing (Lexical Analysis & Syntax Parsing)**
The **JavaScript engine** reads the code in a structured way and it's :

#### **Tokenization (Lexical Analysis)**
- Breaks code into **tokens** (e.g., `var name = "John";` → `var`, `name`, `=`, `"John"`).
#### **Syntax Parsing (AST - Abstract Syntax Tree)**
- Organizes tokens into a **tree-like structure (AST)**.
- Detects **syntax errors** at this stage.

### 3. **Compilation & Execution** (V8 Engine)
after generating AST , the **V8 engine** follows this pipeline:

#### **Interpreter (Ignition)**
- Converts AST into **bytecode** (low-level, platform-agnostic instructions).

#### **Parallel Compilation Paths**

1. **Optimizing Compiler (TurboFan)**
    - Takes frequently executed ("hot") bytecode.
    - Applies **advanced optimizations** (e.g., inlining, dead code elimination).
    - Produces **highly efficient machine code**.
        
2. **Baseline Compiler (SparkPlug)**
    - Converts bytecode to **unoptimized machine code** for immediate execution.
    - Acts as a "quick start" before potential TurboFan optimization.
### 4. **Output**

- The final result is displayed/executed

Here a drawing simplifies all of above explanation:
![[Screenshot_20250804_113418_Samsung Notes.jpg]]

# **Hoisting**
it's a feature in JS that enables JS to be able to define variables before they even declared.

### How?
It's simply takes any declaring line and puts it above usage  
(eg, printing ,operations , etc...)  
Let's take this code as example:



**Case1:** the above lines i put the declaration of "data" before printing so what js did is , took the **Declaration** above printing so js can define it.
```js
//what dev writes:
console.log(data);
var data ;
//outputs : undefined

//what js do (puts ant declaring above usage)
var data ;
console.log(data);
//after hoisting
```

**Case 2:** in this case it shows that js **only takes the declaration** and hoist it even if value is initialized.
```js
console.log(data) ;
var data = 5;

// after being hoisted V

var data;
console. log(data) ;
data = 5 ;
// outputs :undefined
```

**Case 3:** in this case i tried manually to initialize variable before printing so js only do the hoisting for declaration then both initialization and declaration is defined before printing and it works!.

```js
data = 5;
console.log(data) ;
var data;

// vvvvv after hoisting

var data;
data = 5 ;
console.log(data);
//outputs: 5
```

**conclusion :**
js only hoist declarations and nothing else but declarations.

### So what about (let , const) variables ?.


![[Screenshot 2025-07-07 003836.png  | 600]]
- **let Case :** the normal logical error message appears which says "i cant show u an un-initialized variable ! .  
  
- **Const Case :** it's prohibited to not to initialize and declare at the same time.

------------------------------------------------------------------------
# Lexical Grammar :
*Definition of all tokens in the language*

**So What?.. :** lexical grammar or (lexical analysis) is the initial phase in javascript after writing the code where js reads and analayze the code.  
  
**Explaination:** js engine starts to read your code and breaks it into small units called a "**_Token_**_"_ , which means **_the smallest unit in code that can be identified by js_**_._  
  
**How JS do Tokenization? :**  
Let's simply use this example { var data = 5 ; }  
in previous example js takes this line and breaks it into tokens and becomes like.. { "var" "data" "=" "5" ";"}. qoutations doesn't mean strings it's just for clarifying..  
then these tokens grouped togather then called **Syntax** which is a group of tokens  
  
So after code broken down into tokens , grouped by syntaxes , js catigorizes these tokens into a tree-like structure called (AST or Abstract Structuerd Tree) and here where syntax errors caught then proceeds to next compiling phases .  
  
**Important to know:**  
• **Tokens:** smallest unit in the code , can be (identifires , values , keywords , etc ...)  
• **Syntax :** group of forming tokens to get a meaningful code  
• this topic is discussed before on (***mention note here or put link***)

----------

# Arrays 
i will only show what's important in this section
##    What's Arrays?
An array is a data structure that stores multiple values in a single variable. Think of it like a box with numbered compartments (indexes starting from 0).

```javascript
let fruits = ['apple', 'banana', 'orange'];
//            index 0    1        2
```

### Array Types

##### Primitive Arrays
**Definition**: Arrays that store basic data types (numbers, strings, booleans) directly.
```javascript
let numbers = [1, 2, 3, 4, 5];
let names = ['John', 'Alice', 'Bob'];
let flags = [true, false, true];
```
##### Non-Primitive Arrays (Reference Arrays)
**Definition**: Arrays that store objects or complex data structures.
```javascript
let people = [{name: 'John', age: 25}, {name: 'Alice', age: 30}];
let nestedArrays = [[1, 2], [3, 4], [5, 6]];
let mixedTypes = [1, 'hello', {id: 1}, [1, 2]];
```
##    Built-in Functions
### .splice(start index , delete count || null)
This method takes parameters to delete from middle of any array and these parameters are:
- "start index" :which tells from what index to start deleting after
- "delete count": how much to delete and can be null 
For example you have this array;
```js
colors = ["red","blue","green","black","white"];
colors.splice(2,1);
//output : ["red","blue","green","white"]  black got deleted!

//other case 

color.splice(2,2);
//output : ["red","blue","green"] black and white got deleted!
```


### .shift()
This method removes first element from any array and returns the removed element

### .unshift(Element)
This method imports given element to the start of the array

### .join('(any separator)' || null)
this method concatenates all element into one large string , u can choose any separator u like or leave it default

### .concat(array)
literally joins two arrays into one giant array then returns the giant array just created

### .indexOf(value)
searches for elements by value on case I don't know indexes , if not found it will return -1  

### .include(String)
it's a boolean method that does the same thing of **.index()** method

##    Iterative Methods
### .Filter()
The **`filter()`** method of [`Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) instances creates a [shallow copy](https://developer.mozilla.org/en-US/docs/Glossary/Shallow_copy) of a portion of a given array, filtered down to just the elements from the given array that pass the test implemented by the provided function

```js
const words = ["spray", "elite", "exuberant", "destruction", "present"];

const result = words.filter((word) => word.length > 6);

console.log(result);
// Expected output: Array ["exuberant", "destruction", "present"]
```

### find()

**What it does:** Finds the FIRST element that matches your condition **Returns:** The element itself (or undefined if nothing found) **Example:** `array.find(x => x > 5)` **Simple words:** "Give me the first item that fits my rule"

### findLast()

**What it does:** Finds the LAST element that matches your condition  
**Returns:** The element itself (or undefined if nothing found) **Example:** `array.findLast(x => x > 5)` **Simple words:** "Give me the last item that fits my rule"

### findIndex()

**What it does:** Finds the POSITION (index number) of first matching element **Returns:** The index number (like 0, 1, 2...) or -1 if not found **Example:** `array.findIndex(x => x > 5)` **Simple words:** "Tell me WHERE the first matching item is located"

### map()

**What it does:** Transforms each element into something new **Returns:** New array with all elements changed **Example:** `array.map(x => x * 2)` **Simple words:** "Change every item using the same rule"

### flatMap()

**What it does:** Maps each element then flattens the result into one array **Returns:** Single flat array after transformation **Example:** `array.flatMap(x => [x, x * 2])` **Simple words:** "Transform items and squash nested arrays into one flat list"

# Object
in this section I'll discuss about new things u can do JS 
*past knowledge things will be skipped*

### Special things in JS objects:
1. every object can have it's own method 
2. JS are able to make nested objects (not related objects , but objects inside object)

```js
let person = {

    name : "rawi",
    age : 20,
    bloodtype: "B+",
    birth : "26/10/2005",
    
    university : { // nested object
        name : "UQU",
        location : "Makkah",
        speclize : "SWEngineer"
        },
        
	    print_curseWord : function (){
        console.log("fuck you");
         },
		printInfo : function (){
		 let info = this.name + " " + this.age + " " + this.birth //!!
        console.log(info);
		}
} 

console.log(person); // will print all stored data

person.print_curseWord(); // calls in-object method

console.log(person.name); //print specific attribute
  
console.log(person.university.speclize);
// print nested object specific attribute
person.printInfo(); //this will use the printing function inside the object
```

note: i pointed on some line with "!!" to inform that js wont define attribute unless u put "this." before attribute
example
```js
printInfo : function (){
		 let info = this.name + " " + this.age + " " + this.birth 
        console.log(info);
		}
person.printInfo(); // Valid ,will print them with no problems


printInfo : function (){
		 let info = name + " " + age + " " + birth 
        console.log(info);
		}
person.printInfo(); // NotValid , JS wont define values of attributes!
```

### Using bracket notation

in this topic I'll discuss why 
```js 
obj["name"]
```
is more flexible than 
```js
obj.name;
```

#### Cases to use :
1. **Dynamic property access**: it's a way to reach a property using external variables , that has the object property name in it . 
as example:
```js
const user = { name: "John", age: 25 }; 
const X = "name";

// x --> user.name --> name 
// more exaplination: x holds one of user's properties , so when calling with brackets method , it will print the name value stored in user object "indierctly"

console.log(user[x]); //Valid, prints jhon
console.log(user.x); //NotValid , will count x as one of user's properties
```

2. **Property names with special characters** : developer can add special chars on object's properties names and will be called successfully
as example
```js
const user = {
"name-first" : "jhon",
"@ge" : 12 ,
"123" : true
}

console.log(user["@ge"]); //Valid; prints age even if variable naming illegal
```

so it's pretty flexible in cases like this and more i didn't mention , also a special way to remember the rule is :
- `obj.name` = when you know the exact property name at write time
- `obj["name"]` = when the property name is dynamic, has special characters, or comes from a variable.
### Object constructor
 actually there's two types of objects.
 
 1. **object constructor** : which takes input from a parameter and looks like a function more than a class variable.
 2. **object literal** : this simply takes hardcoded data

object constructor be like:
```js
function person (name , age , id){
	
	this.name=name;
	this.id=id;
	this.year=year;

//this method is public for js
	this.greet = function(){
	return "hello mr:" + this.name;
	}

// for some reason , js consider this method as private , it's closure thing i didn't learn yet
	function specialGreet(){
		return "special hello for mr:" +this.name;
	}
}

```

---

# CJS Modules

**What is a module?** : is a separate file contains code that can be imported and reused i other part of the project , or simply *every .js file is a separate module*
Also it's an important role for organizing and structuring code efficiently and break down large projects into smaller components to simplify coding them .

**what does Wrapping mean?** : Node.js put your code as example index.js inside an invisible container which will be explained below

Wrapper container is actually a function that have 5 parameters to run your code :
```js
(function(exports,requierd,module,__filename,__dirname)){
 // your code or runned module will be here 
}
```
**what each parameter means and how they work?** :
- "--dirname": it's the directory name or where the file is located
- "--filename" : the .js file personally path.
- "module": it's the object representing the current file itself
- "export" : shortcut\refrence to module.exports , which is the code shown to public
- "requires": It's the function located in importer file which "brings" code from passed file

**how does exporting works ?** :
export is when u enable public code in a module so any other module can access it .

code and explanation:
```js
//File B
module.export = {value = 42};
//or 
exports.value = 42 ;
```
in this code u just put some code to share with other modules , so here you shared the variable value and it's value 42.

**how does import works?**: 
import when u actually access data from other modules (the enabled code mentioned in exporting). 

code and explanation:

```js
//used .requier() to get another module export
const importedCode = require('./FileB.js');
```
it's like when file A access File B pocket or basket to grab some data


so here's an example on real usage between two files :
file 1(export file) :
```js
//./expermint.js
let person = {
     name: "rawi",
     age :25,
     
    country : {
    name: "Egypt",
    population: 10000000
    },
// some code to pass
// note: u can pass them all in curly braces instead of objects like i did

}

function add (n1 , n2){
        return (n1+n2)+"fuck you";
    }

module.exports = {person,add}; // where code set to public
```

file 2(import class):
```js
//new2.js file
let importedFile = require('./expermint');
//or , cuz u got 2 codes imported so u use 2 placeholders
let {importedObj , importedFunction} = require('./expermint');

// attempt to print what's all in export file
console.log(importedFile);

//attempt to use method imported from ./expermint
let added = add(2,3);
console.log(added);
```

**to keep in mind** : this lesson was for custom files made by developer
you can also use modules made by node.js or known as "built-in files"

let's take as example the operating system module:
```js
let OS = require('os');

  
  

console.log(OS.arch()); //prints somthing with cpu archtictechture (32x , 64x ,etc...)

console.log(OS.cpus()); // prints cpu of user info , i guess it's cores

console.log(OS.hostname()); //name of owners of pc
```


---

# Datatypes

### Undefined & Null
**introduction**
starting with two types of datatypes (*objects , primitive*).
Difference between them is , **primitive** : *holds only one type of data per variable or in another way , it can't assign compound data in one placeholder* , while **object**: can freely have compound datatypes .

there are 7 types of primitive datatypes which is:
- number
- Boolean
- bigInt
- String
- undefined
- null
- symbol --> instructor won't recommend learning this now 

**difference between null and undefined**
	- null:
		- is put on intentionally by dev
	- undefined:
		- it's a default value if no value is assigned
	- both:
		- in comparison between null , undefined result is TRUE ! , while in strict comparison  (compares both datatype and value) result is false 
		- in value they are the same 
there's a loud philosophy about the difference between null and undefined , but for u as developer they're quiet the same.

### Numbers
#### Conversion from int to binary for decimals

in this section i'll discuss some binary shit and focusing on decimal part.
if u given the number with decimal 1920.75 and asked to convert to binary by 
**IEEE 754 binary floating point** .
so now u will split the 1920.75 to 2 parts , the int part which is 1920 and the decimal part which is 0.75 .

**int to binary part** : u take 1920 and divide it by 2 , any divison remains will be the answer so u will keep dividing until reaching zero so it will end up with 
**1920₁₀ = 11110000000**₂.
```txt
1920 ÷ 2 = 960 remainder 0
 960 ÷ 2 = 480 remainder 0
 480 ÷ 2 = 240 remainder 0
 240 ÷ 2 = 120 remainder 0
 120 ÷ 2 =  60 remainder 0
  60 ÷ 2 =  30 remainder 0
  30 ÷ 2 =  15 remainder 0
  15 ÷ 2 =   7 remainder 1
   7 ÷ 2 =   3 remainder 1
   3 ÷ 2 =   1 remainder 1
   1 ÷ 2 =   0 remainder 1
```

**decimal to binary part** : u gonna take 0.75 and multiply it by 2 , and any int 1 appears it will count as one , if not it will be zero like this simple explantion:
```txt
0.75 × 2 = 1.50 → integer part = 1, fractional part = 0.50
0.50 × 2 = 1.00 → integer part = 1, fractional part = 0.00 (stop)
```

the both results combined coming up with :
**1920.75₁₀ = 11110000000.11₂** 
then to convert this binary number to scientific notation will be like:
**1.111000000011E+10**

**lastly presenting on IEEE 754 using decimal formula**:
the formula splitted to :

- sign : which show if the number negative "1" or positive "0" our case is +
  
- exponent: addition the bias (1023 for 64-bit)
  10 + 1023 =1033 
  **1033₁₀ = 10000001001₂**  
  so then the Exponent field will be : **`10000001001`**.

- Mantissa (52 bits) 
  take the fractional part ".111000...." and pad the rest of 40 places with zeros
  so you mantissa should end up with:
	**`1110000000110000000000000000000000000000000000000000`**

-  finally combine them all to represent the 64x so the formula and final result will be:
	  sign , exponent , mantissa
	```txt
	0 10000001001 1110000000110000000000000000000000000000000000000000
	``` 


i didn't focus too much cuz i don't want to , but the important thing is to understand how numbers converted to binary and vice versa

extra things: 
in cases of NaN (Not a Number) will be :
in case of $\sqrt{-16}$ 

any sign | exponents all ones | mantissa anything except all zeros
```txt
0 11111111111 100000000000000000000000000000000
```
note : i just long pressed zeros and ones so it maybe not correct but the important is idea

#### Number Coercion

- when using functions next to primitive variables, actually node.js coercion the variable to object or send the variable to an object and perform the functions needed
  so:
```js
let x = 2,28947;
console.log(x.tofixed(2)); 
// is same as 
console.log(Number.parse(x).tofixed(2));
```
It's actually logicless but node.js is pretty flexible and knows what we want

- it's better to make js make less functions calls and be precise in coding (like the second method of printing fixed number) to decrease calls and resources for an efficient code
#### Number object

in this lecture , the instructor want to teach you how to search in js docs and read about docs , so i will put what is really a valuable information

##### Description (Number object)
JavaScript behaves with literal number (fixed or hardcoded "written by developer in coding time") as it's a float number not as an integer , actually there's no diffrence for js between int and float while other languages makes some differences. 

##### Number Encoding 

**What is IEEE 754 Double-precision ?**:
**IEEE 754** is a standard (a set of rules) that defines how computers store decimal numbers in memory. "Double-precision" means it uses 64 bits (64 zeros and ones) to store each number.

here i will provide how decimal numbers like 9.75 stored in 64bits memory

(I'll make it folded cuz it's gonna take alot place in the note)
###### Complete Example : storing 9.75 in IEE 754 format
 Step 1: Convert to Binary

**Integer part:** `9`

```
9 ÷ 2 = 4 remainder 1
4 ÷ 2 = 2 remainder 0  
2 ÷ 2 = 1 remainder 0
1 ÷ 2 = 0 remainder 1
```

Reading bottom to top: `1001`

**Decimal part:** `0.75`

```
0.75 × 2 = 1.5  → take 1
0.5 × 2 = 1.0   → take 1
0.0 × 2 = 0.0   → take 0 (done)
```

Result: `0.11`

**Combined:** `1001.11`

 Step 2: Normalize to Scientific Notation

- `1001.11` = `1.00111 × 2^3`
- (Moved decimal point 3 positions left)
- Exponent = 3

Step 3: Sign Bit

- Number is positive
- Sign bit = `0`

 Step 4: Calculate Biased Exponent

- Exponent = 3
- Add bias: `3 + 1023 = 1026`
- Convert 1026 to binary:

```
1026 = 10000000010 (11 bits)
```

Step 5: Extract Mantissa

- From `1.00111 × 2^3`, take part after "1.": `00111`
- Pad to 52 bits: `0011100000000000000000000000000000000000000000000000`

 Step 6: Final 64-bit Representation

```
[0] [10000000010] [0011100000000000000000000000000000000000000000000000]
 ↑        ↑                              ↑
sign   exponent                      mantissa
```

**Answer:**

- Sign: `0`
- Exponent: `10000000010`
- Mantissa: `0011100000000000000000000000000000000000000000000000`



##### Number Coercion
here is how js deals with passed values and convert them to number and there's some special cases to return values like :

- undefined turns into "NaN"  means "Not a Number" 
  
- "null" turns into `0`.
  
- `true` turns into `1`; `false` turns into `0`.
  
- Strings are converted by parsing them , if they had numbers will be returned , if not a "NaN" will be returned 
  
- Biglnts throw a "TypeError" to prevent unintended implicit
  coercion causing loss of precision.
  
- Symbols throw a "TypeError" . 
  
- Objects are first converted to a primitive by calling their
  Symbol.toPrimitve() (with "number" as hint), valueOf() ,
  and toString() methods, in that order. The resulting
  primitive is then converted to a number.
  
  
  note :  there are some things left i wouldn't share them here cuz it's kinda waste of time and i can get to them at any time and these topics are:
- static properties
- static methods
- instance properties
- instance methods


# V8 Engine (related to JavaScriptDefining)

## what is an engine (in programming world)?
an engine for any programming language is *the core component that is responsible of executing high level code*.

Note: what's below is a part of a pre written paragraph about JS Journey
## JavaScript execution pipeline(V8)
After parsing high-level JavaScript code through tokenization and syntactic analysis, an **Abstract Syntax Tree (AST)** is generated. This AST is then passed to the interpreter component (in V8, called **Ignition**).

The interpreter converts the AST into **bytecode**, which then branches into two parallel compilation paths:

1. **Optimizing Compiler (TurboFan)**
    
    - Takes bytecode and applies advanced optimizations
        
    - Produces highly efficient machine code
        
    - Used for "hot" functions that run frequently
        
2. **Baseline Compiler (SparkPlug)**
    
    - Converts bytecode directly to machine code without optimizations
        
    - Faster compilation for immediate execution
        
    - Serves as a starting point before potential TurboFan optimization
        

The resulting machine code is then executed by the CPU. This tiered compilation approach balances between quick startup (SparkPlug) and peak performance (TurboFan), with Ignition's bytecode serving as the common intermediate representation.

# NPM (Node.js package manager)

## What's npm ?
**npm(node.js package manager) :** node.js library for simplifying usage of modules whether are public or private.

## Parts of NPM
- **Database and register**: this is the part where npm saves all of it's modules packages that u dont need to understand it.
- **Website** : is the place where u download\publish modules
- **CLI** : just an interface to code commands to npm

## How to use
- Downloading : 
```CLI
npm install <package_name>
```

- Publishing (previous step is mandatory "mentioned below" ):
```
cd <project_folder_name>  // u have to access the folder first
npm publish
```
Important: before publishing u have to perform this command so npm can read the json file created by this command (for versions tracking stuff).

- before publishing (important):
```
cd <project_folder_name>
npm init
```

comment: it's really easy but be careful because npm is wide open-source web that literally anybody can publish malwares. 

# ESM (ECMAScript Modules)

## What is it: 
it's a module system to enable importing from other files 

## Previously:
on previsous lessons we used to import using CommonJS style which looks like this in export\import:
```js
//import in CJS
const myModule = reqiuer('./anyFile.js');

//export in CJS
exports.anyFile = function(){ }
```

## What's new ?
In ESM style there's two ways to import other files , one is using "package.json" defined by npm and the other by just naming the file with (module javascript) or as example "anyFile.mjs"

## Steps (package.json) method

1. let npm initialize the folder using this command:
```
npm init
```
2. then add this  ("type": "module") any where in the file:
```js
// package.json
{
  "name": "expermints",
  "type": "module", // <---- add it here
  "version": "1.0.0",
  "main": "exporter.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "description": ""
}
```
3. then add importing\exporting code on both file u r working on :
```js
//fileA
import * as anyNameA from "./fileB";
//or
import {hello} from "./fileB";

//fileB
export function hello(){}
```
**Notes:**
- you can't import both of CJS and ESM in the same time 
- the npm initializing is mandatory step , otherwise js wont able to access out the current module

## Steps (.mjs extension)
1. For real ! just change the extension from .js to .mjs
2. then start using the exact syntax of ESM importing\exporting

## What's (package.json) ??

**what is `package.json`**: `package.json` is a JSON file found at the root of a Node.js project. It serves as a manifest(بيان او تعريف) for the project, containing metadata and configuration information essential formanaging and running the project.
initialized by opening the terminal on the project folder and typing `npm init`.  

**How it looks like**?: simply it holds name , vesrion , author , type and other things , here's a code showing the json file:

```json
{
  "name": "expermints",
  "version": "1.0.0",
  "main": "exporter.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "description": ""
}
```

# Dates - Unix Epoch 

for full GFG reference about date click the [link](https://www.geeksforgeeks.org/javascript/javascript-date-reference/)
am going to mention here just the important things or pen heads.

**to relax you head** : due to less-sense in JS Date object , u can use NPM and download [`luxon`](https://moment.github.io/luxon/#/tour) package , it's makes sense and easier to use . 

## Unix Epoch :  
This is the number of seconds (or milliseconds) that have elapsed since 00:00:00 UTC on January 1, 1970. 
It's often represented in seconds (10-digit number) or milliseconds in JavaScript (13-digit) as example:
```
1970/01/01 24:00:00 ---> 0
1970/01/01 24:00:01 ---> 1
...
2020/11/21 03:05:00 ---> 1605927900 
```

## ISO 8601

- An **internationally accepted format** for dates and times, structured as 
- **YYYY-MM-DDTHH:mm:ss.sssZ**, where:
    
    - `T` separates date and time,
        
    - `Z` denotes UTC (Zulu time) : UTC means on greenwich time zone 
	    - if ==negative number== instead of z this means geographically left side of greenwich like UTC+4 for USA and Canada 
	    - if ==positive number== this mean geographically right side of greenwich like UTC+3 in KSA and some middle east countries 

## What You Need to Know (Key Notes)

- **Internal precision**: In JS, `Date` stores time as milliseconds since epoch.
	[MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date?utm_source=chatgpt.com)
    
- **Timezone**: `toISOString()` always returns UTC. Local timezone is only used with other getters (e.g. `getFullYear()`). [MDN Web Docs+1](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date?utm_source=chatgpt.com)
    
- **Parsing quirks**(weird formats): `Date.parse()` accepts ISO strings reliably, but not all date strings; avoid ambiguous formats. [momentjs.com](https://momentjs.com/docs/?utm_source=chatgpt.com)
    
- **Epoch limits**: If using 32-bit integers, you’ll hit the **Year 2038 problem**. JS uses 64-bit, so safe—but APIs or databases might not. [Wikipedia](https://en.wikipedia.org/wiki/Unix_time?utm_source=chatgpt.com)[Epoch Converter](https://www.epochconverter.com/?utm_source=chatgpt.com)
    
- **Readability vs size**:
    
    - **Epoch**: compact, fast to process, but not human-readable.
        
    - **ISO 8601**: clear and unambiguous, but longer.

## Date() constructor
```js
new Date()
new Date(value)
new Date(dateString)
new Date(dateObject)

new Date(year, monthIndex)
new Date(year, monthIndex, day)
new Date(year, monthIndex, day, hours)
new Date(year, monthIndex, day, hours, minutes)
new Date(year, monthIndex, day, hours, minutes, seconds)
new Date(year, monthIndex, day, hours, minutes, seconds, milliseconds)

Date()
```
**note**: notice that `monthIndex` starts from 0 not 1 , so month 0=january , 1=feb ,etc ... .


## Big Note: 
you must see the date document to know how to use , because it's so much weird and things wont make since , like `.getDate();` will return the day only . So u must visit the documents
or u can use luxon. 

# Asynchronous

**==This section requires intensive coding and practicing==**
topics is below
## Callbacks in Node.js  
### **0.important to define**
**Async** : requesting something at specific point of time , the response would be in any time later , like emails

**sync** : the opposite of async which means to request something at point of time and the response should be immediately , like phone calls

### **1. Core Concepts**  

#### A. What is a Callback?  
A **callback** is a function passed as an argument to another function, to be executed **later** (usually after an asynchronous task completes).  

#### B. Why Callbacks Exist in Node.js  
Node.js is **single-threaded**, so it uses callbacks to:  
- Avoid **blocking** the main thread.  
- Handle **asynchronous** tasks (file I/O, network requests, timers).  

#### C. How Callbacks Work (Event Loop & Callback Queue)  
1. Async task starts (e.g., `setTimeout`, `fs.readFile`).  
2. Node.js **delegates**(gives) the task to the OS (via `libuv`).  
3. When done, the callback is placed in the **Callback Queue**.  
4. The **Event Loop** moves it to the **Call Stack** when the stack is empty.  

#### 2. Simple Example (With Analogy & Full Code)  

#### 🍰 Analogy: Baking a Cake (Async Task)  
- **Order cake** → Start async task (`setTimeout`).  
- **Give buzzer** → Pass a callback ("call me when done").  
- **Do other work** → Non-blocking execution.  
- **Buzzer rings** → Callback executes.  

#### **📜 Full Code Example**  
```javascript
// 1. Define the callback (what to do when cake is ready)
const eatCake = () => console.log("🎂 Time to eat cake!");

// 2. Async function (takes a callback)
function bakeCake(callback) {
  console.log("🔥 Cake in oven...");
  
  // Simulate async baking (3 seconds)
  setTimeout(() => {
    console.log("⏰ Cake ready!");
    callback(); // Execute the callback
  }, 3000);
}

// 3. Start baking (pass the callback)
bakeCake(eatCake);

console.log("🧼 Washing dishes..."); // Runs immediately!
```
**Output:**  
```
🔥 Cake in oven...
🧼 Washing dishes... 
⏰ Cake ready!
🎂 Time to eat cake!
```  

#### 3. Key Q&A   

#### Q1: What if I don’t pass a callback?  
**A:** Node.js **crashes** if you call `undefined` as a function.  
```javascript
function task(callback) {
  callback(); // ❌ CRASH if no callback passed!
}
task(/*nothing passed here*/); // TypeError: callback is not a function
```  
**Fix:**  
```javascript
task(anyMethod); //this i'll work
```

#### Q2: Does Node.js "know" it’s a callback?  
**A:** No! It’s just a **function parameter**. The "callback" behavior comes from **when/how** it’s called.  

#### Q3: What’s the difference between `func` and `func()`?  
| Syntax       | Meaning                          |
|-------------|----------------------------------|
| `bakeCake(eatCake)` | Passes `eatCake` **as a reference** (callback) |  
| `bakeCake(eatCake())` | Executes `eatCake` **immediately** (wrong!) |  


#### 4. Summary Table  

| Concept            | Explanation                                                          |
| ------------------ | -------------------------------------------------------------------- |
| **Callback**       | Function passed to another function for later execution.             |
| **Async Flow**     | Delegates work → Continues execution → Calls back when done.         |
| **Error Handling** | Node.js uses `(err, data)` callback convention.                      |
| **Event Loop**     | Moves callbacks from **Queue → Call Stack** when the stack is empty. |

**Remember:**  
> "Pass the **recipe** (`eatCake`), not the **finished cake** (`eatCake()`)!" 🍰

#### 5. Next tick

- **Purpose:** Defer (delays) execution of a function until the **current call stack is empty**.

- **Priority:** Higher than `setTimeout(fn, 0)`, `Promises`, or `setImmediate()`.

- **Use Case:** Ensures a task runs _as soon as possible_ without interrupting synchronous code.

Example:
```js
//calling this function will perform this list in-order
/*
1. synchronius work -> print number1 
2. synchrounus work -> print number2 
3. out of sync , now go to async
4. asynchrouns work (next tick is higher priority
   than timer ) -> print "number2 from nexttick"
5. asynchrouns work (done higher priority , 
   now timers is performed) -> print "number2 from nextTick"
6. end
*/

function functioning(){

    console.log("number 1..."); //sync

    setTimeout(() => {
        console.log("number 2... from timer");
    },0); //async 

    process.nextTick( ()=>{ 
    console.log("number 2... from next tick");
    }); //async but higher priority

    console.log("number 3..."); //sync

}
//outputs:
// number 1...
// number 3...
// number 2... from next tick
// number 2... from timer
```


## Event Loop
here am gonna explain role of event loop in node.js 

### **JavaScript Event Loop Explained Simply**

##### **In Plain Words**
The event loop is JavaScript's **task manager** that handles multiple operations without freezing your app. Since JavaScript can only do **one thing at a time** (single-threaded), the event loop smartly organizes tasks to make it feel like multitasking.

##### **What's libUV and it's role**
it's the C++ library the powers node.js ability to handle async tasks without blocking your code.
it has 3 main job's:
1. manages event loops
2. handles async I\O
3. Thread pool for blocking tasks (like timers or anything takes alot of time to be done)

##### **How It Works (Step-by-Step)**
1. **Runs your main code** (synchronous tasks first).
2. **Offloads slow tasks** (like `setTimeout`, file reads) to the background.
3. **Keeps running other code** without waiting.
4. **When slow tasks finish**, their callbacks go into **queues**.
5. **Picks callbacks from queues** and runs them when the main thread is free.

```javascript
console.log("Start");  
  
setTimeout(() => {  
console.log("Timeout callback");  
}, 0);  
  
Promise.resolve().then(() => {  
console.log("Promise resolved");  
});  
  
console.log("End");
```
**Output:**
```
start
end
promise resolved
timeout callback
```

>Note : (this note is after i learnt promises) 
>as u see the promise result was at third. Why ? that because promise have two parts:
>1. `.resolve()` this runs immediately as it's a sync (while there's no async work inside it will be sync)
>2. `.then()` this has two parts i will discuss to be more accurate:
>	1. **CALLING** `.then()` (registering the handler) - SYNC 
>	2. **EXECUTING** the handler function inside .then() - ASYNC
>	   
>	so in conclusion. executing the handler function **is** the async work and makes late work. 
##### **Core components**:
1. **Call Stack:** where main functions(sync side) is executed in order , following LIFO style (Last operation in , First to be executed) 
2. **Web APIs:** it's APIs provided by browsers or Node.js itself 
   fun fact: `setTimeout()` function is a web api (provided by node.js to be honest)
3. **CallBack queues** :  "AKA task queue" holds async functions waiting to run
4. **MicroTask queue** (most priority): higher priority queue for promises  

##### **Event loop queues and how are managed in action time**:

these are all queues in eventloop: 
![[Screenshot 2025-08-16 202757.png]]

and this image shows an example of flow of ordering tasks taken from event loop  
 note: if u tried to count , it will be a useful way to figure out how flow is working  
![[Screenshot 2025-08-16 195243.png|400]]
also this would help :
![[Screenshot 2025-08-16 195221.png|400]]

#### **Queues Inside the Event Loop**

##### **1. Callback Queue (Macro-Task Queue)**
- **Holds:** `setTimeout`, `setInterval`, I/O operations (file reading), UI events.
- **Priority:** Lower than microtasks.
- **Example:** A delivery truck that drops packages (tasks) one by one.
>**==Important Note==** : when using timer queues , when timer ends "**it starts to execute the code after the timer ends**" and not before.

##### **2. Microtask Queue**
- **Holds:** `Promise` callbacks (`then/catch/finally`), `queueMicrotask()`.
- **Priority:** **Highest!** Runs **before** macro-tasks.
- **Example:** An express courier that delivers urgent packages first.

##### **3. NextTick Queue (Node.js Only)**
- **Holds:** `process.nextTick()` callbacks.
- **Priority:** Even higher than microtasks (Node.js specific).

#####  **What Are Promises?**

###### **Simple Definition**
A **Promise** is a **placeholder** for a future value (like a receipt for food you ordered). It can be:
- **Pending** → Waiting for result.
- **Fulfilled** → Success! Got the value.
- **Rejected** → Failed (error).

```javascript
const pizzaOrder = new Promise((resolve, reject) => {
  const isKitchenOpen = true;
  
  if (isKitchenOpen) {
    resolve("🍕 Pizza ready!"); // Success
  } else {
    reject("😢 Kitchen closed!"); // Failure
  }
});

pizzaOrder
  .then((pizza) => console.log(pizza)) // Success handler
  .catch((error) => console.log(error)); // Error handler
```

---

#### **Microtasks vs. Regular Tasks (Macro-Tasks)**

| Feature          | Microtasks                     | Macro-Tasks                  |
|------------------|-------------------------------|-----------------------------|
| **Examples**     | `Promise.then()`, `queueMicrotask()` | `setTimeout`, `setInterval`, I/O |
| **Priority**     | **Runs first!**               | Runs after microtasks        |
| **When Executed**| After current script, **before** next macro-task | After microtasks |

##### **Example Showing Priority**
```javascript
console.log("Start");

setTimeout(() => console.log("Macro-task"), 0); // Goes to Callback Queue

Promise.resolve().then(() => console.log("Microtask")); // Goes to Microtask Queue

console.log("End");
```
**Output:**
```
Start
End
Microtask  ← Runs FIRST (microtask priority)
Macro-task ← Runs after
```


#### **Callback Hell (Pyramid of Doom)**

##### **What It Is**
Nesting callbacks inside callbacks → Makes code unreadable and hard to debug.

##### **Example**
```javascript
getUser(user => {
  getPosts(user, posts => {
    getComments(posts, comments => {
      getLikes(comments, likes => {
        console.log(likes); // 😵‍💫 Deep nesting!
      });
    });
  });
});
```

##### **How to Fix It?**
1. **Use Promises:**
   ```javascript
   getUser()
     .then(user => getPosts(user))
     .then(posts => getComments(posts))
     .then(comments => getLikes(comments))
     .then(likes => console.log(likes));
   ```
2. **Even Better: `async/await`**
   ```javascript
   async function fetchData() {
     const user = await getUser();
     const posts = await getPosts(user);
     const comments = await getComments(posts);
     const likes = await getLikes(comments);
     console.log(likes);
   }
   ```

---

 💡 **Key Takeaways**
✅ **Event Loop** = JavaScript’s task organizer.  
✅ **Microtasks** (Promises) run **before** macro-tasks (`setTimeout`).  
✅ **Callback Hell** = Nested chaos → Fix with **Promises/async-await**.  
✅ **Node.js/Browsers** provide `setTimeout`, **not** core JavaScript.  

Now you understand the event loop like a pro! 🚀



### Recursion  
A function that calls itself until a base case stops it.  
Example:  
```javascript
function countdown(n) {
  if (n === 0) return; // Base case
  console.log(n);
  countdown(n - 1); // Recursive call
}
```


#### **3 Async Flow Control Patterns**  

#### 1. **Sequential**  
Tasks run **one after another**.  
```javascript
function recursiveReader(e) {
  if (e == undefined) return; // Base case
  fs.readFile(`./file${e}.txt`, 'utf8', (err, data) => {
    console.log(data); // Process file
    recursiveReader(folderArr.shift()); // Next file (sequential)
  });
}
// Usage: recursiveReader(folderArr.shift());
```
**Idea**: Each file is read only after the previous one finishes.  

#### 2. **Parallel**  
Tasks run **simultaneously**.  
```javascript
function asyncReading(e) {
  e.forEach(element => {
    fs.readFile(`./file${element}`, 'utf8', (err, data) => {
      console.log(element); // Prints files in random order
    });
  });
}
// Usage: asyncReading([1, 2, 3]);
```
**Idea**: All files start reading at once; order of completion is unpredictable.  

#### 3. **Limited Parallel**  
Run **N tasks at a time** (e.g., 2 parallel reads).  
```javascript
async function limitedParallel(files, limit = 2) {
  const batches = [];
  for (let i = 0; i < files.length; i += limit) {
    batches.push(files.slice(i, i + limit)); // Split into chunks
  }

  for (const batch of batches) {
    await Promise.all(batch.map(file => 
      fs.promises.readFile(`./file${file}`, 'utf8')
    )).then(data => console.log(data));
  }
}
// Usage: limitedParallel([1, 2, 3, 4]);
```
**Idea**: Process files in batches (e.g., 2 at a time) to balance speed/resource usage.  

#### **Flow Control Explained**  
- **Sequential**: Order matters (e.g., pipeline steps).  
- **Parallel**: Speed matters (e.g., API calls).  
- **Limited Parallel**: Control concurrency (e.g., avoid overloading servers).  

Code examples show **how tasks queue/execute** in each pattern.  

#### **Key Takeaways**  
- **Recursion**: Self-calling + exit condition.  
- **Flow Control**:  
  - Sequential = `.then()` or callbacks.  
  - Parallel = `Promise.all()`.  
  - Limited = Batching with `Promise.all`.  

# Memory Management
>this section may have extra things out of the node.js course , the reason of this is this topic is critical to understand well like async topic.

## **Data and memory**

### **what's a Memory**: 
*it's a hardware part that stores data , instructions and information* , temporarily and permanently.
it consists of array of bytes or words(half a byte) with a unique address.

- memory holds both of data and instructions and works beside CPU to provide quick access for data bein used.
- memory management ensures efficient use of memory.

**Memory management** : *an aspect of Operating systems that ensures efficient use of computer memory resource*, basically it controls how memory is allocated and deallocated to processes.

**Why is Memory management required ?**:
- Allocate and de-allocate memory before and after process execution.
- To keep track of used memory space by processes.
- To minimize fragmentation issues.
- To proper utilization of main memory.
- To maintain data integrity while executing of process.

**Clarifying some concepts (optional): **
 - [fragmentation]((https://www.geeksforgeeks.org/operating-systems/what-is-fragmentation-in-operating-system/) : is a problem happens in a memory that u need to see the link to understand it.
 - Allocation : is to specify a portion of the memory for a specific program. 

### **Main Memory**

main memory or also known as RAM(Random Access Memory), is a large array of bytes that the CPU uses to store programs and data being used by the CPU (==same defining as memory but some there's important info here==)
- **RAM is volatile**: meaning it will lost all data saved once power goes off.
- **RAM Size** : it's crucial and may affect CPU performance

### **Logical and Physical address space:** 

- **Logical Address space**: is an address generated by the CPU and also called Virtual address. *it can be defined as the size of the process*.
  
- **Physical Address Space**: *it refers to a set of actual addresses used by the memory hardware*. it's generated by the Memory Management Unit (MMU) through run-time mapping of virtual address.

>To clarify: for some reason programs cant know about actual physical address  , so MMU comes in handy and helps in giving the "alias / nickname" address number for the programs  address number.

### **Static and Dynamic loading**
loading a process into main memory is done by a loader and there's two types of loading:

- **Static Loading:** *Static Loading is basically loading the entire program into a fixed address.* It requires more memory space.
  
- **Dynamic Loading**:  *Dynamic loading loads program routines into memory only when they are needed.* This saves memory by not loading unused routines.

>Dynamic loading analogy: remember the library analogy where it simply says :
	"Don't put every book on your desk. Just keep the one you're reading open and fetch others from the shelf only when you need them."

#### Static and dynamic linking:
To perform a linking task a linker is used. A linker is a program that takes one or more object files generated by a compiler and combines them into a single executable file.

- **Static Linking**: the linker in static combines all necessary program modules into a single executable program. so there's no runtime dependency 

- **Dynamic Linking:** the main idea here is there's a `Stub` are included in each library , this stub's job is to check routinely if the needed function is already in memory or not.
  or in another explaining : instead of loading the whole program in one file like static linking do , instead it leaves the code in a separate file. 

## **Swapping**
is the process of moving "idle" processes from main memory(RAM) to secondary(Disk) to manage limited memory usage.

What Swapping looks like :
![[Screenshot 2025-08-21 222243.png|350]]


## Extra 
// waiting for author to read about memory techniques
// recommended to read (paging , virtual memory , monoprogramming)
for reference click the [link](https://www.geeksforgeeks.org/operating-systems/memory-management-in-operating-system/).

==what after this topic is for node.js course==

## **V8 interacting with memory**
initially each program language engine has it's own in interacting with memory and what to load into stack or heap. for V8 engine it always puts it's placeholder in heap section and their addresses in stack. (some small integers) are sometimes loaded in stack.
![[Screenshot 2025-08-21 225255.png|200]]

>Fact:
	there's a information says the stack section in V8 memory section are for primitive data types like int , float etc... and heap is for objects and that is ==not true.==

### V8 engine memory segmenting
initially the V8 asks the Operating System for some memory , then OS assigns memory with the CPU using component called MMU(Memory Management Unit), then MMU assigns some memory space and returns the logical addresses to the OS. 
![[Screenshot 2025-08-21 225126.png]]

### V8 Resident Set
mention the part u able to modify flags using some code 

every engine of coding language have a special layout and how data is segmented in memory and used by the engine. so in this section i'll explain how memory is segmented , used and some in-action example.

#### **What's a Resident Set Size (RSS)**
The **Resident Set Size** represents the **total portion** of a process's virtual memory that is currently residing in physical RAM. RSS is the **entire memory footprint** that gets segmented into different sections:
```
RSS (Total Physical Memory in RAM)
├── Heap Memory (JavaScript Objects)
│   ├── New Space
|   |   ├── Semi Space
|   |   └── Semi Space
|   |
│   ├── Old Space
|   |    ├── old pointer space
|   |    └── old data space
|   |   
│   └── Large Object Space
├── Code Space (Compiled Code)
├── Stack Memory (Call Stacks)
├── Off-heap Memory (Buffers, External Objects)
└── Other (Metadata, Libraries, OS overhead)
```


>Note: due to a lot of sections in memory i will only mention the most 4 important sections of them 

- **Code Space**: *where executable code is residing and ready to be executed* , also to know this section is **memory protected** means there's no writing or modifying can happen to code.
  
- **New Space**: Initial allocation area for new JavaScript objects and variables. this location is dedicated for new Short-Living Variable.
  
- **Old Space**: *Long-lived objects that survived New Space collections*. here's the section where variables from `New Space` survived and came to `Old Space`, this section includes :
	- **Old Pointer Space**: saves any variables that points to another variables like objects
	- **Old Data Space**: saves any regular variables like `let name = "hi there";`

- **Large Object Space:** Where Object larger than 600 KB is saved.

Some visual info for you about segmented memory:

![[Screenshot 2025-08-25 150537.png|300]]

#### **Action Example on V8 RSS:**

 Code Being Executed:
```javascript
let msg = "start";
let person = {
    birth_year: 2000,
    name: "Rawi"
};
function calc_Age(variable) {
    let current_year = new Date().getUTCFullYear();
    return current_year - variable;
}
console.log(msg);
let age = calc_Age(person.birth_year);
console.log(age);
```

#### **Memory Activity During Execution**

**CODE SPACE**
What Gets Stored with RAM Addresses:

- `0x1586`: Compiled bytecode for `calc_Age` function
- `0x159A`: Script bytecode (main program)
- `0x15AE`: JIT-compiled machine code for optimized `calc_Age`
- `0x15C2`: Function metadata and inline caches

**STACK**
What Gets Stored:

- Function call frames
- Local variables
- Return addresses
- Execution context

Activity Timeline:

1. **Parse Phase**: Script bytecode generated at 0x159A
2. **Initial Execution**: `calc_Age` bytecode stored at 0x1586 (executable, write-protected)
3. **Hot Function Detection**: Function gets optimized after multiple calls
4. **Optimization**: New optimized machine code created at 0x15AE

**Activity Timeline with RAM Addresses:**

```
1. Global Execution Context (Stack Base: 0x7FFF8520)
   ├── 0x7FFF8520: msg → points to 0x2A4C (heap address)
   ├── 0x7FFF8524: person → points to 0x2A60 (heap address)
   └── 0x7FFF8528: calc_Age → points to 0x1586 (code space)

2. calc_Age() Function Call (Stack Frame: 0x7FFF8510)
   ├── 0x7FFF8510: Return address → 0x7FFF8520
   ├── 0x7FFF8514: variable (parameter) → 2000
   ├── 0x7FFF8518: current_year (local) → 2025
   └── 0x7FFF851C: Return value → 25

3. Stack Unwinds
   ├── Frame at 0x7FFF8510 is popped
   └── 0x7FFF852C: age → 25 (back in global context)
```

 #### **HEAP MEMORY**
 
**NEW SPACE** (Young Generation)
**Initial Allocations with RAM Addresses:**
- `0x2A4C`: `"start"` string object
- `0x2A60`: `person` object `{ birth_year: 2000, name: "Rawi" }`
- `0x2A74`: `"Rawi"` string object
- `0x2A88`: `Date` object (temporary)
- `0x2A9C`: `25` number object (result)

**Activity:**
- Fast bump-pointer allocation starting at 0x2A4C
- Objects start here due to generational hypothesis
- Minor GC occurs, promoting survivors to Old Space

 **OLD SPACE** (Old Generation)
**What Gets Promoted with RAM Addresses:**

- `0x4B20`: `"start"` string (promoted after surviving GC)
- `0x4B34`: `person` object (moved from 0x2A60)
- `0x4B48`: `"Rawi"` string (moved from 0x2A74)
- `0x4B5C`: Function objects and their closures

**Activity:**
- Objects that survive multiple minor GCs get promoted here
- Address space starts much higher than New Space
- Less frequent but more thorough garbage collection

 **LARGE OBJECT SPACE**
**What Goes Here:**
- _In this simple example: Nothing_
- Would contain objects > ~32KB (large arrays, big strings, etc.)

**Activity:**
- Direct allocation for oversized objects
- Individual memory page management

####  **Execution Flow with Memory Interaction**

 **Phase 1: Script Loading & Parsing**

```
CODE SPACE: ← Bytecode compilation
STACK:      ← Global execution context created
HEAP:       ← (No allocations yet)
```

 **Phase 2: Variable Declarations**

```
CODE SPACE: 0x159A (Bytecode ready for execution)
STACK:      0x7FFF8520 ← msg points to heap
            0x7FFF8524 ← person points to heap  
            0x7FFF8528 ← calc_Age points to 0x1586
NEW SPACE:  0x2A4C ← "start" string allocated
            0x2A60 ← person object allocated  
            0x2A74 ← "Rawi" string allocated
```

 **Phase 3: Function Execution (calc_Age)**

```
CODE SPACE: 0x1586 ← Function bytecode executed
STACK:      0x7FFF8510 ← calc_Age frame pushed
            0x7FFF8514 ← variable = 2000
            0x7FFF8518 ← current_year = 2025  
            0x7FFF8510 ← return address to 0x7FFF8520
NEW SPACE:  0x2A88 ← Date object allocated (temporary)
            0x2A9C ← Result number 25 allocated
```

 **Phase 4: Garbage Collection (Minor GC)**

```
NEW SPACE:  0x2A88 ← Date object collected (no longer referenced)
            └── Some objects promoted to Old Space
OLD SPACE:  0x4B34 ← person object moved here (from 0x2A60)
            0x4B48 ← Long-lived strings moved here (from 0x2A74)
```

 **Phase 5: Function Optimization** (After multiple calls)

```
CODE SPACE: 0x15AE ← Optimized machine code for calc_Age generated
            0x1586 ← Old bytecode marked for eventual cleanup
STACK:      0x7FFF8510 ← Faster execution with optimized code at 0x15AE
```

####  **Key Memory Patterns**

 **Allocation Strategy:**

- **Short-lived objects** → NEW SPACE (fast allocation)
- **Surviving objects** → OLD SPACE (after GC promotion)
- **Large objects** → LARGE OBJECT SPACE (direct allocation)
- **Executable code** → CODE SPACE (write-protected)

 **Performance Implications:**

- Stack operations are fastest (simple push/pop)
- New Space allocation is very fast (bump-pointer)
- Old Space allocation is more complex but infrequent
- Code Space optimizes hot functions for better performance

 **Memory Pressure Points:**

- Frequent object creation → New Space pressure
- Long-lived object accumulation → Old Space growth
- Complex functions → Code Space expansion
- Deep call chains → Stack overflow risk

#### **Action Example Link**
here's a link of the explaining video link 
(video is stored locally on device).
![[Screen Recording 2025-08-25 152752.mp4]]
### V8 Garbage Collector

#### **What's Garbage Collector**
Is a cleaning service that automatically removes any variables that has no references in code. and there's two types of how garbage collector works:

1. **Manual** : *these kind of coding language requires from the developer to free(remove) some space memory manually.* this kind let's the user manipulate memory like       (C  , RUST).
   
2. **Automatic:** *this kind does the garbage collecting by it's own without any instruction needed from developer.* this kind of GC is fully protected against developer and will never ever let the dev play with memory like (V8 , Java , C# , Go) .

example on manual memory manipulating using C:
![[Screenshot 2025-08-25 193220.png]]

#### **Cons and Pros of GC (Garbage collector)**

Cons: 
- GCs uses CPU to clean the memory , which means it will pause your code do it's work. for Time-sensitive programs it's unacceptable.
   
Pros: 
- some GCs of other languages are light and wont use much CPU power to be executed like (C , Rust). usually these Coding languages are applicable for OS level of programs.

#### **Orinoco (V8's GC)**

Orinoco: is the GC program of V8 engine. 
 it has two sub-systems called:
 
 - Minor GC: works on `New space` section in  memory.
   
 - Major GC: works on `Old Space` Section in memory

##### Minor GC Work(New Space):

it's triggered when cannot allocate data to `New Space`  due to memory full. 
![[Screenshot 2025-08-25 194557.png]]

>Note: i will call semi space A to the one on left and Semi Space B for the one on the right 

Work Flow (Mark - Sweep -Compact):
1. **Mark**: minor scans A section for variables that has references in heap\stack and marks them.
2. **Sweep**: transfers marked variables to section B ==giving them new memory== addresses and removes remaining data in section A.
3. **Compact**: transfers all data in section B back to A ==giving them the old addresses they had before==.
   
>**important to add**:
	if there's any data survived the cleaning process twice , it will be transformed
	to `old space` section.

>Note: if addresses wont updated back , this will cause the program to crash cuz basically the code V8 lost addresses


##### Major GC Work(Old Space):

it has the same steps (mark - sweep - compact)
but with different implementation.

**EXPENSIVE PROCESS**: Major GC is costly because it examines EVERY object in OLD SPACE and makes complex decisions about entire memory pages.

**Pages in old Space and heap:**
old space or heap doesn't have a pool of flowing data , instead it has inner pages with equal sizes. 
according to last V8 update: every page is 1MB.
![[Screenshot 2025-08-25 204218.png|300]]
Data isn't located (side to side \ orderly) inside pages, so it may have some gaps between data so V8 has list called `free_list`  that stores where are these gaps and how much is that gap. so when allocating data to OLD SPACE section, V8 has to look in `free_list` for a free and applicable size for data about to be allocated.
![[Screenshot 2025-08-25 204855.png]]


**Work Flow(Mark-Sweep-Compact with Page Strategy):**

- **Step1** : marking
```
Starting from roots (stack, globals):
Page 1: 0x4B20: person object     [MARKED ✓]
        0x4B34: old string        [MARKED ✓]  
        0x4B48: unused object     [unmarked ✗]
        0x4B5C: cached data       [unmarked ✗]

Page 2: 0x4C00: live data         [MARKED ✓]
        0x4C14: dead object       [unmarked ✗]
        0x4C28: active function   [MARKED ✓]
```

- **Step2**: **Page Analysis & Strategy Decision**
  
  in this phase V8 analyzes each page and chooses different strategies. these strategies are chosen according to pages status which are:
	1. mostly Trash
	2. Mixed content
	3. mostly alive

 **Page Type 1: Mostly Trash (80%+ garbage)**
```
Page 1: [LIVE] [DEAD] [DEAD] [DEAD] [DEAD] [DEAD] [LIVE]
Strategy: EVACUATION
→ Copy 2 live objects to other pages
→ FREE ENTIRE PAGE (instant cleanup)
→ No sweep needed - just abandon the page
```

 **Page Type 2: Mixed Content (40-60% garbage)**
```
Page 2: [LIVE] [LIVE] [DEAD] [LIVE] [DEAD] [LIVE] [LIVE]
Strategy: MARK & SWEEP
→ Keep the page
→ Delete dead objects in place
→ Create free spaces within page
```

 **Page Type 3: Mostly Alive (80%+ live objects)**
```
Page 3: [LIVE] [LIVE] [LIVE] [LIVE] [LIVE] [DEAD] [LIVE]
Strategy: MINIMAL SWEEP
→ Keep page as-is
→ Just remove the few dead objects
→ Minimal work required
```

- **Step3:** Compacting
	This phase is optional and only happens for fragmented pages.

simply all what happens is:
Major GC copies all data in as example page1 to any empty page he has on `free_List` to get rid of gaps fragmentation.

so visually and simply:
before: 
![[Screenshot 2025-08-25 210954.png|300]]
after:
![[Screenshot 2025-08-25 211000.png|300]]


# Operators

## Switch Operator

```js
Switch(variable_name){
	case variable_value:
		logic;
	break;
	
	case variable_value:
		logic;
	break;
	
	default:
		logic;
	}
```

## Spread and Rest Operator


```js
// Rest operator
// REST: Gathers multiple arguments into a single array

function collectNumbers(...allNumbers) {
    console.log("Collected numbers:", allNumbers);
}

collectNumbers(1, 2, 3, 4); // Collected numbers: [1, 2, 3, 4]
```

```js
// SPREAD: Expands array into individual items

const numberList = [1, 2, 3];
const expandedList = [...numberList, 4, 5]; 
/*
what happens :
u kinda pass array "numberList" into array "expandedList"
*/

console.log("Expanded array:", expandedList); // Expanded array: [1, 2, 3, 4, 5]
```

# Passing by Reference \ Value

in this topic i will talk about how addresses behave from different types of passing.
## **Passing by Reference**: 
*is basically ==pointing== to a variable in a heap*.
This type of passing is dedicated to non-primitive types mostly (object ,array, etc.). It's actually how addresses in stack points to the same variable , even if u tried to copy the non-primitive variable , ==it will be pointed and not copied by the new pointer==.
as example:
```js
let arr = [1,2,3];
let NewArr = arr; // copied reference and not data

// these variables are actually pointing to the same data G
```
image visualizing what explained:
![[Screenshot 2025-08-26 201125.png]]

## **Passing by Value**:
*is basically ==copying value== to another variable.* This mostly used by primitive datatypes (strings , int , float , etc...). it's actually about data being copied and have a new space taken in heap.
as example:
```js
let salary = 2000;
let newSalary = salary;
newSalary = 400;

console.log(salary);
console.log(newSalary);
//2000
//400

//new salary took a copy of the data 
```

visualizing:
![[Screenshot 2025-08-26 201909.png]]

## **More explaining using objects:**
in this example there's no copying for data. instead a new reference points to the same values
```js
let person = {
	birth : 2000 , 
	name : "rawi"
};
let newPerson = person; // reference copy
newPerson.birth = 1990;
newPerson.name = "ahmad";

console.log(newPerson);
conosle.log(person);

//{1990 , ahmad}
//{1990 , ahmad}
```

also here if u used spread operator this will lead to copy data 
```js
let person = {
	birth : 2000 , 
	name : "rawi"
};

function doIt (obj){
obj.name = "from func";
obj.birth = 1980;
return obj;
}

console.log(person); 
console.log(doIt({...person})); // copy value
// spread operator will actually make a new obj and perform the function on it and will not point to the old obj


//{ birth: 2000, name: 'rawi' }
//{ birth: 1980, name: 'from func' }

```

>Note:
	when making a string inside of an object and change it later , this actually make a new copy of the string and change pointer to the new string made leading to abandon the old string and waits for garbage collector to sweep it.
	in summary : Strings is immutable! once it created u cant change it.


## **Shallow and Deep Copy**

### Shallow Copy
imagine u were making nested object and trying to copy an object and manipulate it's data and suddenly when u print both original and copied object they have the same "updated" inner objects!. Why? that's because of something in node.js called **Shallow Copy**.
Here's a code visualize what i tried to say:
```js
let person  = {
	name : "rawi",
	age : 20,
	class: {
		id: 123,
		subject: "math"
	}
} //original obj

let newPerson = {...person};
newPerson.name = "ahmad";
newPerson.age = 267;
newPerson.class.id = "321";
newPerson.class.subject = "CS";

console.log(person , person.class);
console.log(newPerson , newPerson.class);

//output:
/*
{ name: 'rawi', age: 20, class: { id: '321', subject: 'CS' } } { id: '321', subject: 'CS' }

{ name: 'ahmad', age: 267, class: { id: '321', subject: 'CS' } } { id: '321', subject: 'CS' }

why rawi's inner class changed too??
*/
```

**Shallow Copy:** means copying the first level from an object.
imagine u copied an object , only the first level will actually be copied and able to be edited manually while inner objects are taken as reference
```
person       --->first level "copied by value"
├── name
├── age
└── class    --->seconde level "copied by reference"
   ├── id
   ├── subject
   └── teacher  ---> third level "copied by reference"
       ├── name
       └── experience
```
this image also shows what happened actually:
![[Screenshot 2025-08-26 232227.png]]


the reason for this problem is the  deep copying we were expecting is intensive. 
and solution is in Deep copy section.

### Deep Copy

The action u were expecting in shallow copying is simply , no fluff , no much talk , a simple global method called  `StructerdClone` !.

```js
let person  = {
	name : "rawi",
	age : 20,
	class: {
		id: 123,
		subject: "math"
	}
}

let newPerson = StructerdClone(person);
newPerson.name = "ahmad";
newPerson.age = 267;
newPerson.class.id = "321";
newPerson.class.subject = "CS";

console.log(person , person.class);
console.log(newPerson , newPerson.class);

//output (as expected ! ):
/*
{ name: 'rawi', age: 20, class: { id: 123, subject: 'math' } }

{ name: 'ahmad', age: 267, class: { id: '321', subject: 'CS' } }

each object has it's own inidividual data!!
*/
```

That's it

# JSON Files

## Define
**JSON file**: **is one of the famous standards to exchange information between servers.**
is stands for **(JavaScript Object Notation)** so it's literally the object notation used in coding but transformed into a message exchanging method.

example :
```json
//this what json looks like
 "person"{
    "name": "rawi",
    "year" : 2005,
    "gender": "male",
    "college": "SWEng"
}

//this is what js objects looks like (u know it but just to compare)
let person = {
    name: "rawi",
    year : 2005,
    gender: "male",
    college: "SWEng"
};
// just the double quotations got removed
```

>Note: 
	u may noticed that the difference between JSON and object notation are really close but a fun fact is u can use the JSON (with the double quotation on the properties) and still works !

**`JSON.parse()` & `JSON.Stringfy()`**

these methods are used to take string (that contains JSON file) and transform it to actual JS object and vice versa to be able to manipulate it \ string it.  

```json
let person =` {
    "name": "rawi",
    "year" : 2005,
    "gender": "male",
    "college": "SWEng"
}`; //notice the backticks ``

let personinjsn = JSON.parse(person);

console.log(person); //shows the String JSON file
console.log(personinjsn); // shows the Object file

/*
outputs:

{
    "name": "rawi",
    "year" : 2005,
    "gender": "male",
	"college": "SWEng"
}

{ name: 'rawi', year: 2005, gender: 'male', college: 'SWEng' }
*/
```

## **How to import**
**using ES6**:
```js
import data from './studentList.json' assert { type: 'json' };

// assert part is important
```

or 

using CJS:
```js
const data = requrie("./studentList.json");
```


# EventEmitter

## What's an EventEmitter?
is a core Node.js class that provides the simple mechanism for **events** and **listeners**. **EventEmitter** follows the **publisher-subscriber pattern** where objects that holds series of functions called "emitters" and the functions assigned to emitters called "listeners".

**What are emitters and listeners and how they work along together**:
- **Emitter**: (publisher) which is the named event
- **Listener:** (subscriber) which is the function runs when the emitter is called

To simply describe how they work along together , imagine the emitter like a medical danger codes, so each medical code(or emitter) has a series of functions to be executed , once a code is called like "codeBlue" , all functions (listeners) subscribed to code blue will start execution.

here's a visual example:
```
codeBlue:
	- task1
	- task2
	- task3

codeBlack:
	- task4
	- task5
	- task6

call("codeBlack");

output:
task4
task5
task6
```


```js
function gropp1(){
	task1()
	task2()
	task3()
}
```
and here's a code explaining that:
```js
import {EventEmitter} from 'events';

const events = new EventEmitter();

function cb1() console.log("locating patient..");
function cb2() console.log("Shocking patient..");

function cb4() console.log("disconectting devices..");
function cb5()console.log("setting defnces..");

events.on('codeBlue' , cb1); 
// .on(); adds listener to emitter
events.on('codeBlue' , cb2);

events.on('codeBlack' , cb4);
events.on('codeBlack' , cb5);
  
events.emit('codeBlue'); 
// .emit() calls the emitter to execute it's listeneres

/*
output

locating patient..
Shocking patient..

*/
```

>Note:
>for simplicity u can say it's kinda of grouping the functions in one container. like the same thing with objects and how they save multiple data in them 



# Promises

## Definition 
you faced previously how hard to deal with callbacks and u may didn't tackle it yet and that's normal. the solution for the callbacks problem are **Promises**.  *Promises are a way to handle asynchronous operations in NodeJS, providing a cleaner and more readable approach compared to* callbacks. They represent the eventual completion (or failure) of an asynchronous operation.

>Note: 
>for misunderstanding the word "eventual" in this lesson , it's basically means in Arabic "بعدين"  

It acts as a placeholder for a result that is not immediately available but will be at some point in the future. Promises help mitigate this issue by providing a more structured way to handle asynchronous operations. Instead of nesting functions, Promises allow chaining of operations, leading to cleaner and more understandable code.
### **Creating a Promise** 
A Promise is created using the new Promise() constructor. The constructor takes a single argument—a function known as the executor function—which is invoked immediately. The executor function takes two arguments:

- **resolve:** A function to call if the asynchronous operation is successful.
- ****reject:**** A function to call if the asynchronous operation fails.

```js
let promise = new Promise ((resolve , reject ) => {
//this function reads a file 
	fs.readFile('./anyFile.txt' , 'utf8' , (err,data)=>{
	 
		if(err){
		// if error occur , error msg will be sent to handler to be shown later
		// using .catch();
			reject(err);  
		}else{
		// otherwise if operation success, result will be sent to handler to 
		// be shown using .then();
			resolve(data);
		}
	})
} )
```

when calling promise to execute their code , the promise executer will do it work immediately, then the result should be passed to one of the handlers (resolve , reject) or otherwise it will be in pending state.
**A Promise can be in one of three states***
- ****Pending:**** The initial state; the asynchronous operation is still in progress.
- ****Fulfilled:**** The operation was completed successfully, and a result is available.
- ****Rejected:**** The operation failed, and an error or reason is provided.

```js
function examplePromise(number){
let promisePause = new Promise((resolve,reject)=>{
	if(number < 0.5){ 
		reject(number);
	}else{
		resolve(number);
	}
}) 
}
// i can show the example using the same method

//pending case : will not show anything cuz i didnt handle .then()
examplePromise(10)
.then();

//success case: will show the result 
examplePromise(10)
.then((data)=>{console.log(data)});

//failed case: will throw an error or handle the rejection (depends on logic)
examplePromise(0.2)
.then((data)=>{console.log(data)})
.catch(()=>{console.log("failed")});

```

## Tips and Guides (by Claude.AI)

### What is a Promise?

A Promise is like ordering food at a restaurant. You place an order (create Promise), the kitchen works on it (async operation), and when it's ready, the waiter brings it to you (`.then()` handler).

**Basic Promise Structure**

```javascript
const promise = new Promise(function executor(resolve, reject) {
    // Your work goes here
    if (/* success */) {
        resolve("Success result");
    } else {
        reject("Error message");
    }
});

promise.then(function handler(result) {
    console.log("Got:", result);
}).catch(function errorHandler(error) {
    console.log("Error:", error);
});
```

### Key Components

 1. The Executor Function

- The function you pass to `new Promise()`
- **Runs immediately** when Promise is created
- Takes two parameters: `resolve` and `reject`

 2. resolve() and reject()

- **`resolve(value)`** = "Success! Here's the result"
- **`reject(error)`** = "Something went wrong! Here's the error"
- These are like **sending messages** to `.then()` and `.catch()`

 3. .then() and .catch() Handlers

- **`.then()`** = receives the value from `resolve()`
- **`.catch()`** = receives the error from `reject()`
- These are like **receiving messages** sent by resolve/reject

### When Does Code Execute?

>Note: u can think of promises is a better way to write codes , it's wrong to think about the promises them self as a async "promise = async". it's just affect by what type of code contains.
>if it has any async code like (reading file , API , DataBase queries , etc...) these will make your code act async , but if no async action it will be all sync.


**Real Example: File Reading**

```javascript
import fs from 'fs';

// Wrap Node.js callback in Promise
function readFilePromise(filename) {
    return new Promise(function(resolve, reject) {
        // Executor starts file reading immediately
        fs.readFile(filename, 'utf8', function(err, data) {
            // This callback runs later (async)
            if (err) {
                reject(err);  // Send error to .catch()
            } else {
                resolve(data); // Send data to .then()
            }
        });
    });
}

// Usage
readFilePromise('./myfile.txt')
    .then(data => {
        console.log("File content:", data);
    })
    .catch(error => {
        console.log("Read failed:", error);
    });
```

### Common Mistakes and Wrong Concepts
#### Wrong Concepts

 Q: **When does Promise code actually start executing?**
**A:** Promise code starts executing **immediately** when you write `new Promise()`, NOT when you call `.then()`. The executor function runs right away.

 Q: **What's the difference between `resolve` and `.then`?**
**A:**
- **`resolve`** = **SENDING** a message ("Here's the result!")
- **`.then`** = **RECEIVING** that message ("I'll handle the result")

Q: **If the executor runs immediately, where's the async part?**
**A:** The **executor** is sync (runs now), but:
- The **work inside** might be async (file reading, timers)
- The **handlers** (`.then`/`.catch`) always run async (later)

 **Q: How do I know if a Promise is sync or async?**
**A:** Ask: "Does the executor wait for something external?"
- **YES** (file reading, API calls, timers) = Async Promise
- **NO** (math, console.log, simple logic) = Sync Promise

Q: ==**What's the point of Promises if I can use callbacks?**==
**A:** Promises solve:
- **Callback hell** (pyramid of nested callbacks)
- **Error handling** (one `.catch()` vs multiple error checks)
- **Multiple async operations** (`Promise.all()`)
- **Readability** (clean, top-to-bottom flow)

#### Mistakes

 1. **Confusion About Timing**

```javascript
// Remember: executor runs NOW, handlers run LATER
const promise = new Promise(resolve => {
    console.log("I run immediately"); // Runs now
    resolve("Done");
});

promise.then(result => {
    console.log("I run later:", result); // Runs after
});
```

 2. **Arrow Functions vs Regular Functions**

Both work the same way:

```javascript
// Arrow functions (shorter)
new Promise((resolve, reject) => {
    resolve("Success");
}).then(result => {
    console.log(result);
});

// Regular functions (longer)
new Promise(function(resolve, reject) {
    resolve("Success");
}).then(function(result) {
    console.log(result);
});
```


### The Flow Explained & Key Terminology

1. **Create Promise** → Executor runs immediately
2. **Executor starts work** → Might be sync or async
3. **Promise created** → Handlers registered with `.then()/.catch()`
4. **Work completes** → `resolve()` or `reject()` called
5. **Handlers run** → Your `.then()` or `.catch()` functions execute

**Key Terminology**
- **Executor** = Function passed to `new Promise()`
- **Handler** = Functions passed to `.then()` and `.catch()`
- **Resolve** = Mark promise as successful
- **Reject** = Mark promise as failed
- **Pending** = Promise is waiting for resolve/reject
- **Fulfilled/Resolved** = Promise completed successfully
- **Rejected** = Promise failedt

### Summary

Promises help you handle async operations cleanly. The executor runs immediately to start the work, but handlers run later when the work completes. Think of `resolve/reject` as sending messages and `.then/.catch` as receiving them.

The key insight: Promise **setup** is sync, but Promise **completion** and **handling** are async.



## Chaining 
Promise chaining allows you to execute asynchronous operations sequentially. Each` .then() `receives the result of the previous one. This creates a readable and maintainable flow for asynchronous code.

here's a real task on chaining , i had a txt file written in JSON style so i made two functions , first is to get the employee by his ID and parse to JSON , second is to write the file into flat file or .txt
```js
function GetEmpID (empID){
    return new Promise((resolve , reject)=>{

        fs.readFile('./EmployeesDB.txt' , 'utf8' , (err,data)=>{
            if(err){
                reject(err);
            }else{
                data = JSON.parse(data); // this should save the result of reading file into JSON
                let customer = data.filter((x)=> x.id == empID); // this should get you the wanted employee
                if(customer.length > 0){ // to make sure not to pass an empty file
                    resolve(customer[0]);
                }else {
                    reject(new Error(`GetEmpID : customer not found`));
                }
            }
        });
    })
}

function ToFlatFile(JsonObj) {
    return new Promise((resolve,reject)=>{
        let flat_Object = "";
        for(const p in JsonObj){
            flat_Object += p +": " + JsonObj[p] + "\n";
        }
            fs.writeFile(`./${JsonObj.name}.txt` , flat_Object,"utf8" ,(err)=>{
                if(err){
                    reject(err);
                }else{
                    resolve();
                }
            })
    })
}

GetEmpID("EMP0056789")
.then((data)=>{ToFlatFile(data)})
.then(()=>{console.log("file is ready");});
// if you had more functions to do , u can add extra .then() as needed
```

>Note:
>- Promises are chained using .then().
>- Each .then() receives the result from the previous promise.
>- .catch() handles any errors in the chain.

## Promise async and wait
### Promise.all()
allows you to execute multiple promises concurrently. It resolves when all promises have resolved, or rejects if any promise rejects.

>Note:
>notice that if any promise rejects it will call failure handler so in other meaning "all pass or nothing pass"

```js
const p1 = new Promise(resolve => {setTimeout(() => {console.log("p1");}, 101);});

const p2 = new Promise(resolve => {setTimeout(() => {console.log("p2");}, 100);});

const p3 = new Promise(resolve => {setTimeout(() => {console.log("p3");}, 2000);});

//trying rejection
const p4 = new Promise((resolve , reject) => {setTimeout(() => {reject("p4 failed")}, 1000);});

Promise.all([p1,p2,p3,p4])
.then(results => {
    console.log("all promises success" , results);
}).catch(
    error=>{console.log("at least one failed", error)}
);

/*
output:
p2
p1
at least one failed p4 failed
p3
*/
```

### Async\Await
When you wrote your first function that returns a promise , it was kind of messy and difficult to read. so Node.js came with `async \ await` to solve this problem by :
- making easier promise creation using `async`
- making easier promise coordination using `await`

an example of code without async\await
```js
function fetchdata(){
	return new Promise((resolve,reject)=>{
	
	setTimeOut(()=>{
	
			resolve("data fetched");
		}, 1000);
	})
}
```

applying async\await on the same code
```js
async function fetchdata(){
	await new Promise(resolve => setTimeOut(resolve,1000));
	return "data fetched";
}
```

>From Author :
>i swear i don't know what i wrote in the example but u will understand it trust me :)

**Using Chains**
even in chaining you only have to return once, look at the code (before-after):
```js
function fetchdata(){
	return fetch("http://someDataToExaplin.com")
	.then(respone=>{
		console.log("first success handler")
		return response;
			}
	)
	.then(data => {
		console.log("got data");
		return data;
	})
	.catch(error=>{
		console.log("error happend")
		throw error;
	})
}


fetchData()
.then(result => console.log('Final result:', result))
.catch(err => console.log('Final error:', err));
```

after applying async\await
```js
async function fetchdata(){
	try{
		let response = await fetch("http://someDataToExaplin.com");
		console.log("got fetch");
		
		let data = await getData(response);
		console.log("got data");
		
		return data
	}catch(error){
		console.log("got an error");
		throw error
	}
}

fetchData()
.then(result => console.log("final result" , result))
.catch(result => console.log("error occur", error));
```

## To keep in mind using Promises 
here i will note every thing that u should pay attention to it and keep it in mind

### using async\await 

using async and await have somethings to keep in mind. one of these things is that u cant use 
`await` in global scope of code like this 
```js
const man = await gettingData();
//wont work

async function gettingData(){
	const man = await anyOtherMethod();
}
//this works fine
```

the reason why is each of async and await are tightly coupled , in other meaning that u cant use `await` without `async`

>Note:
>the real reason why await wont work in global scope is it must be in async scope
>otherwise even Your IDE will tell you it's wrong.
### Using resolve\reject and return
There's some cases to use resolve and reject and there's some cases that you shouldn't do that.

Cases allows resolve\reject:
1.  using it inside executer of promise 

Cases forbids resolve\reject:
1. Don't use resolve inside async functions (they don't exist)
2. Don't use resolve in success handler (inside then, use return instead)
3. Don't use reject in failure handler (inside catch, use throw instead)


# File System functions (not completed - stopped at file Handling)
it's a library designed to operate with files async
i will just revise and add new information.

## **Functions list (Reading):**

### **readfileSync**
`fs.readfileSync(filename , encoding)` : this reads the file synchronous 

 **readfile**
`fs.readfile(filename , encoding , (err,data) => {})` : this reads the file asynchronous for the callback function it will have two parameters (err) which holds the error message is exist. and (data) which carries the result of file reading.

 **ReadFile.promise**
```js
fs.promises.readFile(fileName , encoding)
.then(
	(data) => {}
	(err) =>{}
);
```
this is same as `readFile` but using promises which allows cleaner code instead of callbacks

**ReadFile Using async\await**
```js
async function read(){
	try{
	let data = await fs.promise.readFile(fileName , encoding);
	console.log(data);
	
	}catch(err){
		console.log(err);
	}
}
```
same as promise one but applying async and await , i did the reading file in function because to use async\await u have to include await inside of async scope , await cant be in global scope.

### **createReadStream**
```js
fs.createReadStream(filename , {
	encoding : 'utf8',
	highWaterMark : 3 , 
	// this means how many chars are readen each iteration
	// and by defualt it's 64K
	})
```
actually this function uses eventEmitter to perform what to do on each chunk but keep in mind that there's Pre-defined emitters inside of readStream that you must name them according to what readStream knows , otherwise it will not be called never. 
the internally names defined inside readstream are:
- `open`
- `data`
- `end`
- `close`
- `error

a real example on using them:
```js
import fs from 'fs';

// Create stream - this returns an object that extends EventEmitter
const readStream = fs.createReadStream('./example.txt', 'utf8');

// At this point, Node.js starts reading the file in the background
// but nothing happens yet in your code

// You register listeners for specific events
readStream.on('open', () => {
    // This function will be called WHEN the stream emits 'open'
    console.log('File is now open!');
});

readStream.on('data', (chunk) => {
    // This function will be called WHENEVER data is available
    console.log(`Got ${chunk.length} characters: ${chunk.substring(0, 20)}...`);
});

readStream.on('end', () => {
    // This function will be called WHEN the stream finishes
    console.log('No more data to read');
});

readStream.on('close', () => {
    // This function will be called WHEN the stream closes
    console.log('Stream closed, resources freed');
});

readStream.on('error', (err) => {
    // This function will be called IF there's an error
    console.error('Oops!', err.message);
});

// The magic: Internally, as Node.js reads the file, it calls:
// - this.emit('open') when the file is opened
// - this.emit('data', chunk) for each chunk read
// - this.emit('end') when done
// - this.emit('close') when closed
// - this.emit('error', err) if error occurs
```



## **Function list (Writing):**

### **WriteFileSync**
`fs.writeFileSync(filename , content , encoding)`:
`filename` creates or overwrite the file name  
`content` is what to write inside the new file 

```js
fs.writeFile(filename , content , encoding , (err)=>{
	if(err){
	console.log(err);
	}
})
```
this is the async version on writing files . this one only takes errors as callback , if there's no error the result will be written in other file.

### **WriteStream**
`fs.createWriteStream(filename , encoding)`

this should write the data was readed by **streamReader** .

and this function used to write the data , while the one in the begaining just creates it 
`StreamWriter.write(data)`

### **append**
`fs.appendFile (filename , content , encoding , callback for error)` : this function adds data to existing file instead of overwriting , in case of the file isn't exist it will create a new file.

## **Status and Existence**

### **fs.stat**

`fs.stat(fileName , (err,stat)=>{})`
this function made to check file\folder status. the callback has a parameter `stat` which is an object carries some valuable info , an example of using this function:
```js
fs.stat('./fileNum1' ,(err,stats)=>{
    console.log(stats.isFile());
    // this returns true if the path leads to a file
    console.log(stats.isDirectory());
    // this returns true if the path leads to a folder or directory
})
/*
output:
true
false
*/
fs.stat('readingFiles' ,(err,stats)=>{
    console.log(stats.isFile());
    // this returns true if the path leads to a file
    console.log(stats.isDirectory());
    // this returns true if the path leads to a folder or directory
})
/*
output:
false
true
*/
```

### Copying file into other folder

`fs.copyFile(sourceFolder , DestinationFolder ,(err)=>{})`
simply this takes a file from directory to another directory

as example: 
```js
let Src = 'fileNum1';
let Des = './readingFiles/newFileNum1';

fs.copyFile(Src , Des , (err)=>{
	if(err){
	console.log(err);
	}
})
```



# Timers
we previously discussed about timers in **Async** lesson , but now we gonna go deeper in it this time and define how they work and the difference between timer functions.

## set Immediate
`setImmediate()`:
that schedules a callback to be executed after the current event loop phase completes. 

more explaining example:
```js
function processLargeArray(array) {
    // BAD: This blocks everything!
    for (let i = 0; i < 1000000; i++) {
        // Heavy processing
        doSomethingHeavy(array[i]);
    }
    console.log('Done processing');
}

// Other operations have to WAIT
setTimeout(() => {
    console.log('I want to run but I\'m BLOCKED!');
}, 0);

processLargeArray(bigArray); // Blocks for seconds!

/*
process:

1- largeArray  --> will stop the process for a couple of seconds and not executing anything else other than this heavy iterating fucntion

2- setTimeOut --> prints the message after a time passed
*/
```
this example above was without using `SetImmediate()`

this example includes `SetImmediate()` :
```js
function processHeavyDuty(array , index=0){
	performHeavyWork(array[x]);
	
	setImmediate(()=>{
		processHeavyDuty(array , index+1);
	}); //this will be perfomed next iterantion and not now
}

setTimeOut(()=>{
	console.log("i can talk freely!")
} , 0)
```

>Analogy:
>think of it like a baker making some bread , so he's mixing flour with water , making the dough , put it in the oven . and keeps doing that until flour ends. 
>without using `setImmediate` , even when other work shown up for the baker , he will never do them until he finishes all the flour he got. 
>but with using `setImmediate` , he can freely make other work and get back to baking without letting any thing to wait.

here's also a real example on how to use `setImmediate` correctly:
```js
fs.readFile('huge-file.txt', (err, data) => {
    const lines = data.split('\n');
    const results = [];
    function processNextLine(index) {
        if (index >= lines.length) {
            console.log('File processed');
            return;
        }
        results.push(processLine(lines[index]));
        // Let other operations run!
        setImmediate(() => processNextLine(index + 1));
    }
    processNextLine(0);
});
```

## set TimeOut

**What it does:** Executes a function **ONCE** after a specified delay.
**Parameters:**
```javascript
setTimeout(callback, delay, ...args)
```
- **callback**: Function to execute
- **delay**: Time in milliseconds to wait
- **...args**: Optional arguments to pass to the callback

**Examples:**
```javascript
// Basic usage
setTimeout(() => {
    console.log('This runs after 2 seconds');
}, 2000);

// With arguments
setTimeout((name, age) => {
    console.log(`Hello ${name}, you are ${age}`);
}, 1000, 'John', 25);

// Store reference to cancel
const timeoutId = setTimeout(() => {
    console.log('This might not run');
}, 3000);

clearTimeout(timeoutId); // Cancel it
```

## set Interval

**What it does:** Executes a function **REPEATEDLY** at specified intervals.
**Parameters:**
```javascript
setInterval(callback, interval, ...args)
```
- **callback**: Function to execute repeatedly
- **interval**: Time in milliseconds between executions
- **...args**: Optional arguments to pass to the callback

**Examples:**
```javascript
// Basic usage - runs every 1 second
const intervalId = setInterval(() => {
    console.log('This runs every second');
}, 1000);

// With arguments
setInterval((message) => {
    console.log(message);
}, 2000, 'Hello every 2 seconds');

// Stop after 10 seconds
setTimeout(() => {
    clearInterval(intervalId);
    console.log('Interval stopped');
}, 10000);
```


# Object Oriented Programming

## What's OOP

**OOP**: is a style of programming that uses classes and objects to model real-world things like data and behavior

**OOP consists of:**

- **Encapsulation***: ensures that one team can change data representation and algorithms without causing other team's code change.
- **Polymorphism***:  allows objects to behave differently using the same interface.
- **Inheritance**: The capability of a class to derive properties and characteristics from another class is called Inheritance.

## How to make a Class (code)

### Regular Class 
```js
// Class definition
class Car {
  constructor(brand, model) {
    this.brand = brand; // property
    this.model = model; // property
  }


  // method
  showDetails() {
    console.log(`This car is a ${this.brand} ${this.model}.`);
  }
}

// Creating objects from the class
const car1 = new Car("Toyota", "Corolla");
const car2 = new Car("Honda", "Civic");

// Using the objects
car1.showDetails(); // This car is a Toyota Corolla.
car2.showDetails(); // This car is a Honda Civic.
```

### Encapsulation 

adding hashtag `#` before any method or property makes it private
```js
class Car {
	
	#Secert = "this shit is top secret"; // this propert is secret
  constructor(brand, model) {
    this.brand = brand; // property
    this.model = model; // property
  }
  
  get Secret(){ // this keyword is a getter 
	  return this.#Secret ;  
  }
  
  set Secret(msg){
	  this.#Secret = msg;
  }

  // method
  showDetails() {
    console.log(`This car is a ${this.brand} ${this.model}.`);
  }
  
  #showDetailsPrivate() { // also this is private
    console.log(`This car is a ${this.brand} ${this.model}.`);
  }
}

// Creating objects from the class
const car1 = new Car("Toyota", "Corolla");
const car2 = new Car("Honda", "Civic");

// Using the objects
car1.showDetails(); // This car is a Toyota Corolla.
car2.showDetails(); // This car is a Honda Civic.

// ------------------------------
const person = require("./person.js");
let per1 = new person("rawi" , "19" , "male" , Secret);
per1.printDetails();
//outputs:
// error , secret cant be accesed
```

### Inheritance and Polymorphism
```js
class Car {
  constructor(brand, model) {
    this.brand = brand; // property
    this.model = model; // property
  }


  // method
  showDetails() {
    console.log(`This car is a ${this.brand} ${this.model}.`);
  }
}
// ---
class siddan extends Car{ //inherts super class
	constructor(major , birth){
		this.major = major ; 
		this.birth = birth ; 
	}
	
	showDetails(){ //applied polymorphism
		console.log(`
		brand ${this.brand}
		model ${this.model}
		major ${this.major}
		birth ${this.birth}
		`)
	}
}
```

### Closures 

**Closure**:  is when a function remembers variables from it's outer scope 

>note:
>Think of it like this: the inner function has access to variables from outside, and it "closes over" them (that's why it's called closure).

example:
```js
function outer(x){
    x=5;
    function inner(y){ //this function can see x from oute scope
        return x+y;
    }
    return inner;
}

const func = outer();
console.log(func(2));
// outputs: 7
```

# Regular Expression (RegExp) 

>important note:
>due to difficulty and useless of this lesson , i made this lesson as a refrence so u can under stand what each part do and u can visit GFG (link provided somewhere here) to see more 

## RegExp intro & char classes 
**regular expression**: is a special sequence of characters that defines a search pattern, typically used for pattern matching within text.  often used for validating email addresses , phone numbers , checking if string contains certain patterns. Also its a string algorithm.

Types of usage:
1. to check a string if contains things or specific strings
2. text extraction , like extracting a string from string same as `subString()`

like in this code am validating if a specific string is exist or not 
```js
let temp = /rawi/   // searching for "rawi"
let tempi = /rawi/i  // same but case-insensetive

let str1 = "rawi loves hhis wife too much"
let str2 = "wife loves her Rawi too much"

console.log(temp.test(str1));// case senstive 
console.log(temp.test(str2));

console.log(tempo.test(str1)); // case not senstive
console.log(tempo.test(str2));

/*
outputs:

true
false  because of  "Rawi" instead of "rawi"

true
true
*/

```

Ways to use RegExp:
1. using `literal` (same as example above)
   `let reg = /pattern/i` 
2. using an object
   `let reg = new RegExp(string , flag)`
   like:
   `let reg = new RegExp("pattern" ,"i")`

>note:
>using literal method won't be flexible because as you see it's hardcoded , while the object method will be flexible to use.  but also can be mixed. by using literal inside object parameter 

**Dealing with strings and flags**

Starting with a simple example (not real code):
```js
let RegExp = /cat/

let text=
`
The fat cat ran down the street.
It was searching for a mouse to eat
`

/*
output:  
cat
*/

// ------------------------------
let RegExp = /the/g 
// "g" means global. to match multiple times

let text=
`
The fat cat ran down the street.
It was searching for a mouse to eat
`
 
// "the" is found 1 time (case senstive)

// -----------------------------

let RegExp = /the/gi 
// "g" means global. to match multiple times
// "i" means case-insenstive. to make case insenstive
// can be combined to get them globally

let text=
`
The fat cat ran down the street.
It was searching for a mouse to eat
`

// "the" found 2 times
```

==due to difficulty of RegExp , i will provide a link as refrence on GFG==
[link](https://www.geeksforgeeks.org/javascript/javascript-regexpregular-expression/)

## example 1 validating email

and here's a reference example on extracting emails from an array

```js
// ORIGINAL REGEX BREAKDOWN
let regex = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,6}$/;

/*

PART BY PART EXPLANATION:

^                    = Start of string (anchor)
[a-zA-Z0-9._-]       = Character class: letters, digits, dot, underscore, hyphen
    a-z              = lowercase letters (a to z)
    A-Z              = uppercase letters (A to Z)  
    0-9              = digits (0 to 9)
    .                = literal dot (period)
    _                = underscore
    -                = hyphen
+                    = One or more of the preceding characters
@                    = Literal @ symbol (required in email)
[a-zA-Z0-9.-]        = Character class: letters, digits, dot, hyphen
    a-z              = lowercase letters
    A-Z              = uppercase letters
    0-9              = digits
    .                = literal dot
    -                = hyphen
+                    = One or more of the preceding characters
\.                   = Literal dot (escaped because . is special in regex)
[a-zA-Z]             = Character class: only letters (no digits in TLD)
    a-z              = lowercase letters
    A-Z              = uppercase letters
{2,6}                = Between 2 and 6 characters (.com = 3, .museum = 6)
$                    = End of string (anchor)
  
WHAT EACH SECTION MATCHES:
^[a-zA-Z0-9._-]+     = Username part (before @)
@                    = The @ symbol
[a-zA-Z0-9.-]+       = Domain name part (after @ before final dot)
\.                   = The final dot before extension
[a-zA-Z]{2,6}        = Top-level domain (.com, .org, .co.uk, etc.)
$                    = Ensures entire string matches (no extra characters)
*/
  
// SHORTCUT VERSION USING \w
let shortcutRegex = /^\w+[\w.-]*@[\w.-]+\.\w{2,6}$/;
  
/*
SHORTCUT EXPLANATION:
  
\w = [a-zA-Z0-9_] (letters, digits, underscore)
   Note: \w does NOT include dot or hyphen by default!


^\w+                 = Start with one or more word characters
[\w.-]*              = Zero or more word chars, dots, or hyphens
@                    = Literal @ symbol
[\w.-]+              = One or more word chars, dots, or hyphens  
\.                   = Literal dot
\w{2,6}              = 2-6 word characters for TLD
$                    = End of string

Why [\w.-]* instead of just \w+?
- First char must be alphanumeric (\w+)
- Following chars can include dots/hyphens ([\w.-]*)
- This prevents starting with dot or hyphen
*/
```

## example 2  validating password 

```js
// Password Validation Regex
// Rules: At least 12 chars, 2 uppercase letters, 2 special characters
const passwordRegex = /^(?=(.*[A-Z]){2,})(?=(.*[!@#$%^&*()\-_=+{};:,<.>]){2,}).{12,}$/;

// Test the regex
console.log(passwordRegex.test('SecureP@ssw0rd!!')); // true
console.log(passwordRegex.test('weakpass'));        // false

/*

REGEX BREAKDOWN:

/^................................................$/
  ^    = Start of string
  $    = End of string

(?=(.*[A-Z]){2,})
  ?=   = Positive lookahead (condition must be met)
  .*   = Any characters (zero or more)
  [A-Z] = Single uppercase letter
  {2,}  = At least 2 occurrences
  → Must contain at least 2 uppercase letters

(?=(.* [!@#$%^&*()\-_=+{};:,<.>] ){2,})
  ?=   = Positive lookahead
  .*   = Any characters
  [...] = Character class containing special characters
  {2,}  = At least 2 occurrences
  → Must contain at least 2 special characters

.{12,}
  .     = Any character
  {12,} = At least 12 occurrences
  → Must be at least 12 characters long

*/


```


# HTTP
## Define

HTTP (HyperText Transfer Protocol) is a protocol that defines how messages are formatted and transmitted between web servers and clients (like browsers). It's the foundation of data communication on the World Wide Web.

**URI, URL, and URN differences:**

- **URI (Uniform Resource Identifier)**: The general term for any string that identifies a resource. It's like an address system for web resources.
    
- **URL (Uniform Resource Locator)**: A type of URI that tells you both what the resource is AND where to find it. It includes the protocol (http://) and location.
    
    - Example: `https://example.com/users/123`
- **URN (Uniform Resource Name)**: A type of URI that only identifies what the resource is, but not where to find it. Like a person's name - it identifies them but doesn't tell you their address.
    
    - Example: `urn:isbn:1234567890`

**URI Structure:** `scheme "://" authority path "?" query "#" fragment`

- **scheme**: The protocol used (http, https, ftp)
- **authority**: Contains the server information
    - **userinfo**: Username and password (optional)
    - **host**: Domain name or IP address
    - **port**: Port number (optional, defaults to 80 for http, 443 for https)
- **path**: The specific resource location on the server
- **query**: Additional parameters (starts with ?)
- **fragment**: Specific section within the resource (starts with #)

> example:
> `https://www.youtube.com/watch?v=dQw4w9WgXcQ#t=30s`

For your Information:
![[Screenshot from 2025-10-01 22-37-24.png]]
### HTTP Methods

#### What each method does:

- **GET**: Requests data from a server. Used to retrieve information without changing anything.
- **POST**: Sends data to create new resources on the server.
- **PUT**: Sends data to update/replace an entire resource.
- **DELETE**: Removes a resource from the server.

### HTTP Request Examples:

**GET Request:**

```
GET /api/users/123 HTTP/1.1                // Method, path, and HTTP version
Host: example.com                           // The server domain name
Accept: application/json                    // What type of data the client wants
User-Agent: Mozilla/5.0                     // Information about the client software
```

**POST Request:**

```
POST /api/users HTTP/1.1                    // Method, path, and HTTP version
Host: example.com                           // The server domain name
Content-Type: application/json              // Type of data being sent in the body
Content-Length: 45                          // Size of the data in bytes

{                                           // Request body (the actual data)
  "name": "John Doe",
  "email": "john@example.com"
}
```

### HTTP Response Example:

```
HTTP/1.1 200 OK                             // HTTP version and status code
Content-Type: application/json              // Type of data being sent back
Content-Length: 67                          // Size of the response data
Date: Wed, 25 Sep 2025 10:00:00 GMT        // When the response was created

{                                           // Response body (the actual data)
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com"
}
```

## Creating Server 
**how does it work in node.js**: once you included the HTTP module you will have to `createServer()` which returns an object from type : Server .
when calling the `createServer()` you will pass two things :
- request 
- response
- callback function 
so in handy will look like this:
```js
import as http from 'HTTP';

const server = http.createServer((req , res) = >{
	res.write("this"); 
	//if u have bigger data needed to be seperated into chunks u will use .write
	 //but you have to follow it with a .end  
	res.end("this is respond body");
	//ends respond and sends it 
 });
 
 server.listen(8000 , callback)
 //function that runs the server 
 // .listen(port , callback will run when a request arrive)
```
so in previous code i made a server object than i ran it on port 8000.
What i noticed that when i access network panel on developertools in browser i can see that actually there's two requests:
![[Screenshot from 2025-09-29 01-18-12.png]]
- local host : which is my actual website
- favicon.ico: something trivial and doesnt make sense to be honset

- **Preventing other requests**
so to prevent requesting to `favicon` i have simply to logic that prevents favicon to be requested and returns 404 for it 

```js
import as http from 'HTTP';

const server = http.createServer((req , res) = >{
	if(req.url == "./favicon.ico"){
		res.statuscode = 404;
		res.statusMessage = 'Not Found';
		res.end();
		return;
	}
	// assuming you know basic http and u know what is this means
	
	res.write("this");   
	res.end("this is respond body");
	 
 });
 
 server.listen(8000 , callback)
 
```
now it's denying `favicon`

- **dealing with headers**
also another thing that you can deal with requests via request headers 
so like in this code which prints the request header
```js
import * as http from 'http';

const server = http.createServer((req , res)=>{
	console.log(req.headers);
	// here ^
	if(req.url == "/favicon.ico"){
		res.statusCode = 404;
		res.statusMessage = 'Not Found';
		res.end();
		return;
	}
	res.write("this is from ");
	res.end("first server ever");
})


server.listen(8000 , ()=>{console.log("request arrived");})

//results:
/*
{
  host: 'localhost:8000',
  connection: 'keep-alive',
  'cache-control': 'max-age=0',
  'sec-ch-ua': '"Chromium";v="140", "Not=A?Brand";v="24", "Brave";v="140"',
  'sec-ch-ua-mobile': '?0',
  'sec-ch-ua-platform': '"Linux"',
  'upgrade-insecure-requests': '1',
  'user-agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/140.0.0.0 Safari/537.36',
  accept: 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,
  'sec-gpc': '1',
  'accept-language': 'en-US,en;q=0.8',
  'sec-fetch-site': 'none',
  'sec-fetch-mode': 'navigate',
  'sec-fetch-user': '?1',
  'sec-fetch-dest': 'document',
  'accept-encoding': 'gzip, deflate, br, zstd'
}
{
  host: 'localhost:8000',
  connection: 'keep-alive',
  'sec-fetch-site': 'same-origin',
  'sec-fetch-mode': 'no-cors',
  'sec-fetch-dest': 'empty',
  'user-agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/140.0.0.0 Safari/537.36',
  'accept-encoding': 'gzip, deflate, br, zstd',
  'accept-language': 'en-US,en;q=0.9'
}
*/
```
so based on the result we have in the server , i can tell by reading the json object that it's came from a browser ( you can see "brave" , encoding , ....)
so i can put a validation point in my logic to prevent any thing comes from browser (maybe i want all requests from bash only)

- **Showing difference between browser request and bash (or any) request**

Browsers request:
![[Screenshot from 2025-09-29 01-57-40.png]]

Bash request (using curl):
![[Screenshot from 2025-09-29 02-02-30 1.png]]

## Handling Multi Path and QueryString
![[Screenshot from 2025-10-01 22-37-24.png]]
This image above will show how a request is sent to server and extract

## Basic Authorization
initially when dealing with servers via http , all data transfers aren't protected so an initial way to protect data is using **credentials**
- **Credentials** : pieces of information, such as a username and password or session tokens, that a user or application provides to a server to verify their identity for accessing protected resources. 
So these credentials works really good but not safe 100% even if encrypted (badguys may have the key encryption and bust everything). another method to secure data transferring is using **tokens**.
- **Tokens**: a digital, ==**temporary** credential== used in token-based authentication to verify a user's identity to a server without repeatedly sending passwords.

another type of Tokens "access tokens" is called **Bearer Token** *which is a type of access tokens used to authorize and access resources to web apps and APIs* 
how to use it:
- **Obtain a bearer token**: before using a bearer token u have to obtain one from an authentication server. which involves some other authorization first like sending credentials to get your own bearer token 
- **Send the request**: with involving your authorization in the header set (in the http request) , then send your HTTP request, it will hold the authorization in header set as said to be authorized automatically without inputting user credentials each time.
- **Server Validation**: once server receives your request , it will extract the token from authorization token from the header then validates the token to ensure it's legitimate (permissible) and hasn't expired , lastly grants access to  the request




## Claude's Full HTTP Server&Client guide


### Core Concepts (Theory)

#### Buffers

**What are Buffers?**
Buffers are objects that represent raw binary data (bytes). They store data in its most basic form - as sequences of numbers (0-255).

**Example:**
- Text "Hello" in Buffer: `<Buffer 48 65 6c 6c 6f>`
- Each number represents one character in binary form

**Why Buffers exist:**
Network communication works with binary data, not text. When data travels over the internet, it's sent as raw bytes. Buffers let Node.js work with this binary data efficiently.

**Converting between Buffer and String:**
```javascript
const buffer = Buffer.from('Hello');  // String to Buffer
const text = buffer.toString();       // Buffer to String → 'Hello'
```

#### Chunks

**What are chunks?**
Chunks are small pieces of data that arrive separately over the network. Instead of receiving all data at once (like receiving a whole book), you receive it page by page.

**Why chunks exist:**
Data traveling over the network is split into small packets because:
- Network bandwidth is limited
- Large files need to be broken down
- Makes communication more efficient

**What chunks look like:**
Each chunk is a Buffer object containing a portion of the total data.

```
Total data: "Hello World, this is a long message"

Chunk 1: <Buffer 48 65 6c 6c 6f>           → "Hello"
Chunk 2: <Buffer 20 57 6f 72 6c 64>        → " World"
Chunk 3: <Buffer 2c 20 74 68 69 73>        → ", this"
```

**Processing chunks:**
1. Collect all chunks in an array
2. Combine them using `Buffer.concat()`
3. Convert to string if needed

#### Streams and Events

**What are Streams?**
Streams are objects that let you read or write data piece by piece (in chunks), rather than all at once. Both request and response objects in Node.js HTTP are streams.

Think of a stream like water flowing through a pipe - it flows continuously in small amounts, not all at once.

**Official Documentation - Streams:**
https://nodejs.org/api/stream.html

**Stream Events:**

Events are signals that fire when something happens. Streams emit several events:

**'data' event:**
- Fires each time a new chunk arrives
- Called multiple times (once per chunk)
- Provides one chunk (Buffer) as parameter

**'end' event:**
- Fires once when all data has been received
- Called only one time
- No parameters
- Safe to process the complete data now

**'error' event:**
- Fires if something goes wrong
- Provides error object as parameter
- Examples: network failure, invalid data, connection refused

**Timeline example:**
```
Start receiving data
    ↓
'data' event → chunk 1
'data' event → chunk 2
'data' event → chunk 3
    ↓
'end' event → complete
```

#### HTTP Headers

**What are headers?**
Headers are metadata (information about data) sent along with HTTP requests and responses. They don't contain the actual content, but describe it.

Think of headers like the information written on an envelope - it tells you about what's inside without opening it.

**Two types of headers:**

**1. Request Headers** (Client → Server)
Information the client sends to tell the server what it wants or what it's sending.

**2. Response Headers** (Server → Client)
Information the server sends to tell the client about the response.

**Common standard headers:**
- `Content-Type`: Format of data (we'll discuss MIME types below)
- `Content-Length`: Size of data in bytes
- `Authorization`: Authentication token
- `Cache-Control`: How long to cache the response
- `Set-Cookie`: Cookie data to save in browser

**Custom headers:**
Headers made by developers for specific needs:
- Often start with `X-` prefix (though this is deprecated)
- Not part of HTTP standard
- Examples: `X-Powered-By`, `X-Request-ID`

**Official Documentation - HTTP Headers:**
https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers

**Official Registry:**
https://www.iana.org/assignments/http-fields/http-fields.xhtml

#### MIME Types (Content-Type Header)

**What are MIME types?**
MIME (Multipurpose Internet Mail Extensions) types tell the receiver what format the data is in. They're used in the `Content-Type` header.

**Format:** `type/subtype`

**Common MIME types:**
- `application/json` - JSON data
- `text/plain` - Plain text
- `text/html` - HTML document
- `image/jpeg` - JPEG image
- `image/png` - PNG image
- `application/pdf` - PDF file
- `application/octet-stream` - Generic binary file (unknown type)

**Example usage:**
```javascript
// Telling receiver "I'm sending JSON"
res.setHeader('Content-Type', 'application/json');

// Telling receiver "I'm sending an image"
res.setHeader('Content-Type', 'image/jpeg');
```

**Why it matters:**
The receiver needs to know what format the data is in to process it correctly. Without the correct MIME type, browsers and clients might misinterpret the data.

**We'll see MIME types used throughout the code examples in the Server and Client sections.**

**Official Documentation - MIME Types:**
https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types

#### HTTP Response Structure

Every HTTP response has two parts that arrive separately:

**Part 1: Headers** (arrives first)
```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 27
Date: Sat, 18 Oct 2025
                        ← Empty line separates headers from body
```

**Part 2: Body** (arrives after headers)
```
{"message": "Hello World"}
```

This separation is important because it affects when callbacks and events fire in your code.

#### Data Flow Between Client and Server

Understanding how data flows helps you know when to use chunks, buffers, and events.

**Client Sending to Server:**
```
CLIENT SIDE:
1. JavaScript object: { name: 'Ahmed' }
2. JSON.stringify() → '{"name":"Ahmed"}'
3. req.write(string) → Node.js converts to Buffer
4. req.end() → sends request
   ↓
   ═══ Network (data split into chunks) ═══
   ↓
SERVER SIDE:
5. 'data' event fires → receives Buffer chunks
6. Store chunks in array
7. 'end' event fires → all received
8. Buffer.concat(chunks) → one Buffer
9. .toString() → '{"name":"Ahmed"}'
10. JSON.parse() → { name: 'Ahmed' }
```

**Server Responding to Client:**
```
SERVER SIDE:
1. JavaScript object: { status: 'ok' }
2. JSON.stringify() → '{"status":"ok"}'
3. res.setHeader() → set headers
4. res.end(string) → Node.js converts to Buffer, sends headers then body
   ↓
   ═══ Network (data split into chunks) ═══
   ↓
CLIENT SIDE:
5. Callback fires → headers received
6. Access statusCode, headers
7. 'data' event fires → receives Buffer chunks
8. Store chunks in array
9. 'end' event fires → all received
10. Buffer.concat(chunks) → one Buffer
11. .toString() → '{"status":"ok"}'
12. JSON.parse() → { status: 'ok' }
```

**Key principle:** Data always travels as Buffers (binary), gets split into chunks, and must be collected and combined before processing.

---

### Server Concepts (Deep Dive)

#### How createServer Works

`http.createServer()` creates a server object that listens for incoming HTTP requests.

**The requestListener callback:**
- Takes two parameters: `(request, response)`
- Executes **every single time** a request arrives
- Each request gets its own separate execution
- The callback doesn't know about previous or future requests

**Official Documentation:**
https://nodejs.org/api/http.html#httpcreateserveroptions-requestlistener

#### The Request Object (Server Side)

The request object (`req`) is a **Readable Stream** containing all information about the client's request.

**Key properties:**
- `req.method` - HTTP method (GET, POST, PUT, DELETE)
- `req.url` - Path requested (/login, /api/users)
- `req.headers` - Object with all request headers (keys are lowercase)
- `req.httpVersion` - HTTP version (1.1, 2.0)

**Official Documentation:**
https://nodejs.org/api/http.html#class-httpincomingmessage

#### The Response Object (Server Side)

The response object (`res`) is a **Writable Stream** used to send data back to the client.

**Key methods:**
- `res.setHeader(name, value)` - Sets a response header (before sending body)
- `res.statusCode` - Property to set HTTP status code
- `res.write(data)` - Sends a chunk of body (can be called multiple times)
- `res.end([data])` - Finishes response, closes connection (MUST be called)

**Important rule:** You must call `res.end()` for every request, or the client hangs forever.

#### Receiving Request Body

When clients send data (POST/PUT), it arrives as chunks through events.

**The pattern:**
1. Listen to 'data' event - collect Buffer chunks in array
2. Listen to 'end' event - combine chunks, convert to string, parse if JSON
3. Listen to 'error' event - handle failures

#### Routing

Routing means directing requests to different handlers based on URL and/or method.

Use conditional statements (`if`, `switch`) to check `req.url` and `req.method`.

---

### Server Code Examples

#### Echo Server (Complete Example with Data Flow)

This server receives data and sends it back unchanged - perfect for understanding the flow.

```javascript
import http from 'http';

const server = http.createServer((req, res) => {
  
  if (req.url === '/echo' && req.method === 'POST') {
    
    let body = [];  // Array to collect chunks
    
    // Event: each time a chunk arrives
    req.on('data', (chunk) => {
      body.push(chunk);  // Store the chunk
      console.log('Chunk received:', chunk.length, 'bytes');
    });
    
    // Event: all data received
    req.on('end', () => {
      // Combine all chunks into one Buffer
      body = Buffer.concat(body);
      
      console.log('Total received:', body.length, 'bytes');
      
      // Echo: send back exactly what was received
      res.setHeader('Content-Type', 'text/plain');
      res.end(body);  // Send Buffer directly
    });
    
    // Event: error occurred
    req.on('error', (err) => {
      console.error('Error:', err);
      res.statusCode = 500;
      res.end('Error receiving data');
    });
  }
});

server.listen(8000, () => {
  console.log('Server running on port 8000');
});
```

**Data Flow Diagram for Echo Server:**
```
CLIENT                          SERVER
  |                               |
  | POST /echo                   |
  | Body: "Hello World"          |
  |----------------------------->|
  |                              | 'data' event → chunk: <Buffer 48 65...>
  |                              | Store in array
  |                              | 'data' event → chunk: <Buffer 6c 6c...>
  |                              | Store in array
  |                              | 'end' event
  |                              | Combine chunks → full Buffer
  |                              | Prepare response
  |                              |
  |     Headers (200 OK)         |
  |<-----------------------------|
  |     Body: "Hello World"      |
  |<-----------------------------|
  |                              |
```

#### Basic Server with Routing

```javascript
import http from 'http';

const server = http.createServer((req, res) => {
  
  // Route based on URL
  switch (req.url) {
    case '/':
      res.end('Home Page');
      break;
      
    case '/api/data':
      // Route based on method
      if (req.method === 'GET') {
        res.setHeader('Content-Type', 'application/json');
        res.end(JSON.stringify({ data: [1, 2, 3] }));
      } else {
        res.statusCode = 405;  // Method not allowed
        res.end('Only GET allowed');
      }
      break;
      
    default:
      res.statusCode = 404;
      res.end('Not Found');
  }
});

server.listen(8000);
```

#### Receiving JSON Data

```javascript
// Only the important POST handling part
if (req.url === '/api/users' && req.method === 'POST') {
  
  let body = [];
  
  req.on('data', chunk => body.push(chunk));
  
  req.on('end', () => {
    try {
      // Combine chunks and convert to string
      body = Buffer.concat(body).toString();
      
      // Parse JSON string to object
      const userData = JSON.parse(body);
      
      console.log('User:', userData.name, userData.age);
      
      // Send JSON response
      res.setHeader('Content-Type', 'application/json');
      res.end(JSON.stringify({
        success: true,
        message: 'User created'
      }));
      
    } catch (error) {
      // Handle invalid JSON
      res.statusCode = 400;
      res.end('Invalid JSON');
    }
  });
}
```

#### Complete Server Example

```javascript
import http from 'http';

const server = http.createServer((req, res) => {
  
  console.log(`${req.method} ${req.url}`);
  
  switch (req.url) {
    case '/':
      res.setHeader('Content-Type', 'text/plain');
      res.end('Welcome!');
      break;
      
    case '/api/data':
      if (req.method === 'GET') {
        // GET: Return data
        res.setHeader('Content-Type', 'application/json');
        res.end(JSON.stringify({ items: [1, 2, 3] }));
        
      } else if (req.method === 'POST') {
        // POST: Receive data
        let body = [];
        
        req.on('data', chunk => body.push(chunk));
        
        req.on('end', () => {
          try {
            // Parse received JSON
            body = Buffer.concat(body).toString();
            const data = JSON.parse(body);
            
            console.log('Received:', data);
            
            // Respond with success
            res.setHeader('Content-Type', 'application/json');
            res.end(JSON.stringify({ 
              success: true,
              received: data 
            }));
          } catch (err) {
            res.statusCode = 400;
            res.end('Invalid JSON');
          }
        });
        
        req.on('error', (err) => {
          console.error('Error:', err);
          res.statusCode = 500;
          res.end('Server Error');
        });
        
      } else {
        res.statusCode = 405;
        res.end('Method Not Allowed');
      }
      break;
      
    default:
      res.statusCode = 404;
      res.end('404 Not Found');
  }
});

server.listen(8000, () => {
  console.log('Server running on port 8000');
});
```

---

### Client Concepts (Deep Dive)

#### How http.request() Works

`http.request()` creates an HTTP request to a server and returns a request object.

**Parameters:**
- `options` (Object) - Configuration: hostname, port, path, method, headers
- `callback` (Function) - Called when response headers arrive (NOT when body complete)

**Official Documentation:**
https://nodejs.org/api/http.html#httprequestoptions-callback

**Official Guide:**
https://nodejs.org/en/learn/modules/making-http-requests-with-nodejs

#### When the Callback is Called

The callback `(response) => { }` runs when the server sends back the **response headers**, NOT when the response body is complete.

**Timeline:**
```
1. req.end() → Request sent
2. Server processes
3. Server sends headers
   ↓
   ⚡ CALLBACK FIRES (can access statusCode, headers)
   ↓
4. Server sends body chunks
   ↓
   'data' events fire
   ↓
5. Body complete
   ↓
   'end' event fires
```

#### The Response Object (Client Side)

When making a request, the response object is a **Readable Stream** containing the server's response.

**Key properties:**
- `response.statusCode` - HTTP status (200, 404, 500)
- `response.headers` - Object with all response headers

#### The Request Object (Client Side)

The request object returned by `http.request()` is a **Writable Stream** used to send data.

**Key methods:**
- `req.write(data)` - Sends data (can call multiple times)
- `req.end([data])` - Finishes request, **actually sends it** (MUST call)

**Critical rule:** You MUST call `req.end()` or nothing gets sent.

#### Request Options

The `options` object configures the request:

**Required:**
- `hostname` - Server address (domain or IP, no protocol)
- `port` - Port number
- `path` - URL path (starts with `/`)
- `method` - HTTP method (uppercase: 'GET', 'POST', etc.)

**Optional:**
- `headers` - Object with request headers
- `timeout` - Milliseconds before timeout

---

### Client Code Examples

#### Basic GET Request

```javascript
import http from 'http';

const options = {
  hostname: 'localhost',
  port: 8000,
  path: '/api/data',
  method: 'GET'
};

const req = http.request(options, (res) => {
  console.log('Status:', res.statusCode);
  
  let data = [];
  
  // Collect response chunks
  res.on('data', chunk => data.push(chunk));
  
  // Process complete response
  res.on('end', () => {
    data = Buffer.concat(data).toString();
    console.log('Response:', data);
  });
});

// Handle errors
req.on('error', err => console.error('Error:', err.message));

// Send request (no body for GET)
req.end();
```

#### POST Request with JSON

```javascript
import http from 'http';

// Prepare JSON data to send
const postData = JSON.stringify({
  name: 'Ahmed',
  age: 25
});

const options = {
  hostname: 'localhost',
  port: 8000,
  path: '/api/users',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': Buffer.byteLength(postData)
  }
};

const req = http.request(options, (res) => {
  console.log('Status:', res.statusCode);
  
  let responseData = [];
  
  res.on('data', chunk => responseData.push(chunk));
  
  res.on('end', () => {
    responseData = Buffer.concat(responseData).toString();
    console.log('Server response:', responseData);
  });
});

req.on('error', err => console.error('Error:', err.message));

// Send data and close request
req.write(postData);
req.end();
```

#### Complete Client Example

```javascript
import http from 'http';

// Data to send
const data = JSON.stringify({
  username: 'ahmed',
  email: 'ahmed@example.com'
});

// Configure request
const options = {
  hostname: 'localhost',
  port: 8000,
  path: '/api/login',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': Buffer.byteLength(data)
  }
};

// Create and send request
const req = http.request(options, (res) => {
  
  // Callback fires when headers arrive
  console.log('Status:', res.statusCode);
  console.log('Content-Type:', res.headers['content-type']);
  
  let responseData = [];
  
  // Collect response body chunks
  res.on('data', (chunk) => {
    responseData.push(chunk);
  });
  
  // Process complete response
  res.on('end', () => {
    responseData = Buffer.concat(responseData).toString();
    
    try {
      // Try parsing as JSON
      const jsonResponse = JSON.parse(responseData);
      console.log('Parsed response:', jsonResponse);
    } catch (e) {
      console.log('Response (not JSON):', responseData);
    }
  });
});

// Handle errors
req.on('error', (err) => {
  console.error('Request failed:', err.message);
});

// Set timeout (optional)
req.setTimeout(5000, () => {
  console.log('Request timeout');
  req.destroy();
});

// Write data and send
req.write(data);
req.end();
```

---

### Best Practices

#### Always Handle Errors

```javascript
// Server
req.on('error', (err) => {
  res.statusCode = 500;
  res.end('Error');
});

// Client  
req.on('error', (err) => {
  console.error('Failed:', err);
});
```

#### Always Call end()

```javascript
res.end();  // Server - close response
req.end();  // Client - send request
```

#### Use Buffer.concat() Not String Concatenation

```javascript
// ❌ Wrong
let body = '';
req.on('data', chunk => body += chunk.toString());

// ✅ Correct
let body = [];
req.on('data', chunk => body.push(chunk));
req.on('end', () => body = Buffer.concat(body).toString());
```

#### Validate JSON Parsing

```javascript
try {
  const data = JSON.parse(body);
  // Process data
} catch (error) {
  res.statusCode = 400;
  res.end('Invalid JSON');
}
```

#### Set Appropriate Status Codes

```javascript
res.statusCode = 200;  // OK
res.statusCode = 201;  // Created
res.statusCode = 400;  // Bad Request
res.statusCode = 404;  // Not Found
res.statusCode = 500;  // Server Error
```

---

### Additional Resources

#### Official Node.js Documentation

**HTTP Module:**
https://nodejs.org/api/http.html

**Making HTTP Requests Guide:**
https://nodejs.org/en/learn/modules/making-http-requests-with-nodejs

**Anatomy of HTTP Transaction:**
https://nodejs.org/en/learn/modules/anatomy-of-an-http-transaction

**Streams:**
https://nodejs.org/api/stream.html

#### Additional References

**HTTP Headers (MDN):**
https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers

**HTTP Status Codes (MDN):**
https://developer.mozilla.org/en-US/docs/Web/HTTP/Status

**MIME Types (MDN):**
https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types

**IANA Registry:**
https://www.iana.org/assignments/http-fields/http-fields.xhtml

## More from O'Reilly book

### What are MIMEs

MIME (Multipurpose Internet Mail Extensions) types tell the browser or server what kind of data is being sent. They're like labels that say "this is a JSON file" or "this is an image".
was placed to fix the server inefficiency due to shock from datatype sometimes

**Example:**

- `application/json` - for JSON data
- `text/html` - for HTML pages
- `image/png` - for PNG images
- `text/plain` - for plain text files
- 