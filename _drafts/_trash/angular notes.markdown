#yo, to install a generator4
yo
yo doctor

#bower commands
$bower list
$bower search <dep>
#the save is so we update the bower.json
$bower install --save <dep>..<depN>

#installing all the tools through node
$npm install -g yo bower grunt-cli gulp

#install and update yeoman
$npm install -g yo

#creating a new angular project
$npm install -g generator-angular
$yo angular

#some stub generators
$yo angular:controller myController
$yo angular:directive myDirective
$yo angular:filter myFilter
$yo angular:service myService
$yo angular:route routeName

#bower
$bower search <dep>
$bower install <dep>..<depN>
$bower update <dep>

#RequireJS (grunt bower) vs NoRequireJS (grunt wiredep)

#Grunt development
$grunt serve
#Grunt run tests
$grunt test
#grunt build the app
$grunt
#grunt run the build app
$grunt serve:dist
