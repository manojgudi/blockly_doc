gnu_lc
---
Forked from blockly http://code.google.com/p/blockly/

###Blockly Overview

Blockly is just a translational program; i.e. it converts visual programming blocks in to equivalent javascript/python using javascript parser. Since blockly has multiple js files, it builds all the js files into smaller and dense *compressed.js files which are then used by blockly applications.
Closure is a dependency required by blockly to build those javascript files.
<br/>
###Getting started
First, clone two repositories<br/>
Blockly source code:<br/>
`git clone https://github.com/manojgudi/blockly`

And Closure Library:<br/>
`git clone https://github.com/manojgudi/closure-library-read-only.git`

Blockly source code has *build.py* builder script but it wont execute unless the folder structure conforms to [Blockly Folder Structure](http://code.google.com/p/blockly/wiki/Closure)<br/>

We'll have to build javascript files later when creating custom blocks. Also we'll be specifically concentrating on language generation application provided by blockly -> *apps/code/en.html* 


###Creating Custom blocks

To create custom blocks its imperative that developer should be clear about functionality which he expects from it. Creating a custom block also usually means that a new category is required for toolbox. 

Let's take an example of building a simple custom block *my_hpf()* function with one input and output. There are _ steps:<br/>

1. Editing en.html to introduce new category

In our case we introduce a new category called **Filters**

```
diff --git a/apps/code/en.html b/apps/code/en.html
index 2fde103..e62e710 100644
--- a/apps/code/en.html
+++ b/apps/code/en.html
@@ -6,6 +6,12 @@
   <script>
     var MSG = {
       // Tooltips.
@@ -21,6 +27,7 @@
       catColour: 'Colour',
       catVariables: 'Variables',
       catProcedures: 'Procedures',
+      catFilters: 'Filters',
```


One might alternatively refer [original documentation](http://code.google.com/p/blockly/wiki/CustomBlocks) of blockly.