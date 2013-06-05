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


One might alternatively refer [original documentation](http://code.google.com/p/blockly/wiki/CustomBlocks) of blockly.