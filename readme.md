gnu_lc
---
Forked from blockly http://code.google.com/p/blockly/

###Blockly Overview

Blockly is just a translational program; i.e. it converts visual programming blocks in to equivalent javascript/python using javascript parser. Since blockly has multiple js files, it builds all the js files into smaller and dense *compressed.js files which are then used by blockly applications.
Closure is a dependency required by blockly to build those javascript files.
<br/>

###Requirements
1. LAMP or XAMP with write permission on /var/www/
2. git
3. Lots of patience

###Getting started
First, clone two repositories<br/>
Blockly source code:<br/>
`git clone https://github.com/manojgudi/blockly.git`

And Closure Library:<br/>
`git clone https://github.com/manojgudi/closure-library-read-only.git`

Blockly source code has *build.py* builder script but it wont execute unless the folder structure conforms to [Blockly Folder Structure](http://code.google.com/p/blockly/wiki/Closure)

Put entire root folder _blockly_ inside /var/www/.. so that it runs on apache. 

We'll have to build javascript files later when creating custom blocks. Also we'll be specifically concentrating on language generation application provided by blockly -> *apps/code/en.html* 


###Creating Custom blocks

To create custom blocks its imperative that developer should be clear about functionality which he expects from it. Creating a custom block also usually means that a new category is required for toolbox. 

Let's take an example of building a simple custom block *my_hpf()* function with one input and output. There are _ steps:<br/>

####Editing en.html to introduce new category

In our case we introduce a new category called **Filters**; here's the diff, only the line **catFilters: 'Filters',** was added: <br/>
[(How to read diffs)](http://stackoverflow.com/questions/2529441/how-to-work-with-diff-representation-in-git)

```
diff --git a/apps/code/en.html b/apps/code/en.html
index 2fde103..e62e710 100644
--- a/apps/code/en.html
+++ b/apps/code/en.html
@@ -21,6 +27,7 @@
       catColour: 'Colour',
       catVariables: 'Variables',
       catProcedures: 'Procedures',
+      catFilters: 'Filters',
```

####Adding Category to template.soy

Changing layout of *en.html* is a two step process. <br/>
The layout represented by *en.html* is sourced from *apps/code/template.js* and *apps/code/template.js* is built from template.soy

So, first edit template.soy and define number blocks your category will contain; in our Filter Category we have just one block called my_hpf

```
diff --git a/apps/code/template.soy b/apps/code/template.soy
index ef1ec64..a556c5a 100644
--- a/apps/code/template.soy
+++ b/apps/code/template.soy
@@ -208,6 +208,13 @@
         </value>
       </block>
     </category>
+ 
+
+    <category name="{$ij.MSG.catFilters}">
+      <block type="filters_hpf"></block>
+    </category>   
+    
+
     <category name="{$ij.MSG.catColour}">
       <block type="colour_picker"></block>
       <block type="colour_random"></block>
```

Then compile this template.soy script using SoyToJsSrcCompiler<br/>
(assuming your working directory is *~/blockly/static/*)<br/>
```java -jar apps/code/soy_comp/SoyToJsSrcCompiler.jar --outputPathFormat apps/code/template.js --srcs apps/code/template.soy```

If there are no warnings or error, then compilation is done successfully.

####Defining block properties
The [official documentation](http://code.google.com/p/blockly/wiki/DefiningBlocks) provides good overview.<br/>
Here's diff for out my_hpf filter block. Create a new filter.js in specified folder.<br/>

```
diff --git a/language/common/filters.js b/language/common/filters.js
new file mode 100644
index 0000000..50f0e6c
--- /dev/null
+++ b/language/common/filters.js
@@ -0,0 +1,11 @@
+Blockly.Language.filters_hpf = {
+  helpUrl: 'http://www.example.com/',
+  init: function() {
+    this.setColour(290);
+    this.appendValueInput("NUM")
+        //.setCheck("null")
+        .appendTitle("My HPF");
+    this.setOutput(true, "null");
+    this.setTooltip('');
+  }
+};
```<br/>

####Scripting for block -> Python parsing
Go through these links before commencing<br/>
[Generating Code](http://code.google.com/p/blockly/wiki/GeneratingCode)<br/>
[Operator Precedence](http://code.google.com/p/blockly/wiki/OperatorPrecedence)

Create a new filter.js file in directory specified. Code is pretty much self-explanatory<br/>

```
diff --git a/generators/python/filters.js b/generators/python/filters.js
new file mode 100644
index 0000000..d036856
--- /dev/null
+++ b/generators/python/filters.js
@@ -0,0 +1,22 @@
+// Python Filters.js
+'use strict';
+
+Blockly.Python = Blockly.Generator.get('Python');
+
+Blockly.Python.filters = {};
+
+Blockly.Python.addReservedWords('hpf');
+
+Blockly.Python.filters_hpf = function() {
+
+       
+       var argument0 = Blockly.Python.valueToCode(this, 'NUM', Blockly.Python.ORDER_ATOMIC) || '0';
+/*     if (isNaN(argument0)){
+               argument0 = 0;
+       }
+*/     var code = 'my_hpf(' + argument0 + ')' ;
+       var order = code < 0 ? Blockly.Python.ORDER_UNARY_SIGN: Blockly.Python.ORDER_NONE;
+       return[code, order];
+
+};
+
```

####Including scripts in en.html
And finally include both these scripts in en.html

```
diff --git a/apps/code/en.html b/apps/code/en.html
index 2fde103..e62e710 100644
--- a/apps/code/en.html
+++ b/apps/code/en.html
@@ -6,6 +6,12 @@
   <script type="text/javascript" src="/storage.js"></script>
   <script type="text/javascript" src="../_soy/soyutils.js"></script>
   <script type="text/javascript" src="template.js"></script>
+
+  <!-- My Scripts -->
+  <script type="text/javascript" src="../../language/common/filters.js"></script>
+ <script type="text/javascript" src="../../generators/python/filters.js"></script>
```

####Building js scripts
Building js files is necessary to merge and compress filters.js scripts with other js files. With working directory _~/blockly/static/_ <br/>
```./build.py``` <br/>
**CAVEAT**<br/>
build.py is network dependent, it is imperative that you use this [build.py](https://github.com/manojgudi/blockly/blob/proxy_network/build.py) with proper proxy IP and port settings to build successfully<br/>
Change the following line:<br/>
`proxy_support = urllib2.ProxyHandler({"http":"http://10.101.11.108:3128"})
`

The common error encountered with build failure is *JSONDecodeError: No JSON object could be decoded*; which means incorrect network settings.

Once built, the *apps/code/en.html* should display your block in your category, and blocks should be parsed in to python code as a result.


One might alternatively refer [original documentation](http://code.google.com/p/blockly/wiki/CustomBlocks) of blockly.