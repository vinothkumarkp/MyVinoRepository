# Ember Migration - 2.3.0 to 3.3.0

### Improvement
We have few advantages moving from 2.3.0 to 3.3.0

* [glimmer-2](https://madhatted.com/2016/11/30/5-things-to-know-about-ember-2-10-and-glimmer-2)
    - Fast rendering engine.
    - Lightweight in-memory representation of the DOM.
    - 30% reduction of their gzipped (CIDE) application JS payload from 2.1MB to 1.2MB.
* [codemod](https://medium.com/@Dhaulagiri/embers-javascript-modules-api-b4483782f329)
    - new Javascript Modules API
* [Simplify source compilation]()
    - bower-components removed from dependencies. Nodemodules only used.
    - javascript hint(jshint - .jshintrc) removed. Using [eslint](https://eslint.org/) javascript linting.

### Code Changes

`Controller Usage`
```js
/* Before - import controller */
export default Em.Controller.extend({
}); 
```
```js
/*After - import controller */
import Controller from '@ember/controller';
export default Controller.extend({
});
```
`Component Usage`
```js
/* Before - import component */
export default Em.Component.extend({
});
```
```js
/* After - import component */
import Component from '@ember/component';
export default Component.extend({
});
```

`Route Usage`
```js
/* Before - import route */
export default Em.Route.extend({
});
```
```js
/* After - import route */
import Route from '@ember/routing/route';
export default Route.extend({
});
```

`Router.js Code Usage`
```js
/* Before - Router dynamic mapping Usage*/
Router.map(function() {
    this.route('zoho/files', {path: "filetree/:repoType/:branchName/*filePath"});
});
```
```js
/* After - Router dynamic mapping Usage*/
Router.map(function() {
    this.route('zoho/files', {path: "filetree/:repotype/:branchname/*filepath"});
    /**Or Snack-Case**/
    this.route('zoho/files', {path: "filetree/:repo_type/:branch_name/*file_path"});
    /**
    * Not Use Duplicate Route Mapping below like this
    * this.route('zoho/files', {path: "filetree/:repoType/:branchName"});
    * this.route('zoho/files', {path: "filetree/:repoType/:branchName/*filePath"});
    **/
});
```

`Mixin Usage`
```js
/* Before - import mixin */
export default Em.Mixin.extend({
});
```
```js
/* After - import mixin */
import Mixin from '@ember/object/mixin';
export default Mixin.create({
});
```

`emberUtils (<APPNAME>/utils/emberutils.js-create) - Ember Functions Usage`

 `Ember utils function - Usage`
 ```js
/* Before - Ember Util function usage */
export default Em.Controller.extend({
    helloworld: function() {
        var a = <xxx>;
        if(Em.isNone(a)) { console.log("welcome") };
        if(Em.isEmpty(a)) { console.log("welcome") };
        if(Em.typeOf(a)) { console.log("welcome") };
    }
});
```
```js
/* After - Ember Util function usage */
import emberUtils from 'cide/utils/emberutils';
import Controller from '@ember/controller';
export default Controller.extend({
    helloworld: function() {
        var a = <xxx>;
        if(emberUtils.isNone(a)) { console.log("welcome") };
        if(emberUtils.isEmpty(a)) { console.log("welcome") };
        if(emberUtils.typeOf(a)) { console.log("welcome") };
    }
});
```

`Ember Array function - Usage`
```js
/* Before - Ember Array function usage */
export default Em.Controller.extend({
    arrayProxyCreate: Em.ArrayProxy.create({ content: [] })
    array: { content: Em.A([]) },
    arrayList: Em.makeArray(filename)
});
```
```js
/* After - Ember Array function usage */
import emberUtils from 'cide/utils/emberutils';
import Controller from '@ember/controller';
export default Controller.extend({
    arrayProxyCreate: emberUtils.ArrayProxyCreate({ content: [] })
    array: { content: emberUtils.A([]) },
    arrayList: emberUtils.makeArray(filename)
});
```
`Ember Object - Usage`
```js
/* Before - Ember Object usage */
export default Em.Controller.extend({
    helloworld: function() {
        var objCreate = Ember.Object.create({a:1, b:2});
        var objExtend = Ember.Object.extend({a:1, b:2});
        Em.set(getCurrNavbarItem, 'isActive', true);
        Em.setProperties(getCurrNavbarItem, {
            'isActive': true,
            'isEdit': false
        });
    }
});
```
```js
/* After - Ember Object usage */
import emberUtils from 'cide/utils/emberutils';
import Controller from '@ember/controller';
export default Controller.extend({
    helloworld: function() {
        var objCreate = emberUtils.objectCreate({a:1, b:2});
        var objExtend = emberUtils.objectExtend({});
        emberUtils.set(getCurrNavbarItem, 'isActive', true);
        emberUtils.setProperties(getCurrNavbarItem, {
            'isActive': true,
            'isEdit': false
        });
    }
});
```

`Ember computed function - usage`
```js
/* Before - Ember Computed function usage */
export default Em.Controller.extend({
    sortedRepos: Ember.computed.sort('repositoriesList', 'sortDefinition'),
    selectedbpointItems: Em.computed.filterBy('bpointItems.content', 'selected', true)
});
```
```js
/* After - Ember Computed function usage */
import emberUtils from 'cide/utils/emberutils';
import Controller from '@ember/controller';
export default Controller.extend({
    sortedRepos: emberUtils.computedSort('repositoriesList', 'sortDefinition'),
    selectedbpointItems: emberUtils.computedFilterBy('bpointItems.content', 'selected', true)
});
```
`Ember inject controller - usage`
```js
/* Before - Ember inject controller usage */
export default Em.Controller.extend({
    appCtrl: Em.inject.controller('application'),
    codeEditorCtrl: Em.inject.controller('code/editor')
});
```
```js
/* After - Ember inject controller usage */
import emberUtils from 'cide/utils/emberutils';
import Controller from '@ember/controller';
export default Controller.extend({
    appCtrl: emberUtils.injectController('application'),
    codeEditorCtrl: emberUtils.injectController('code/editor')
});
```

`Ember helper - usage && htmlSafe - usage`
```js
/* Before - Ember helper & htmlSafe usage */
export default Em.Helper.helper(function(params, args) {
    return Em.String.htmlSafe("<b>Helloworld</b>");
});
```
```js
/* After - Ember helper & htmlSafe usage */
import { helper as buildHelper } from '@ember/component/helper';
import emberUtils from 'cide/utils/emberutils';
export default buildHelper(function(params, args) {
    return emberUtils.htmlSafe("<b>Helloworld</b>");
});
```
`Ember runloop - usage`
```js
/* Before - runloop usage */
export default Em.Component.extend({
    didInsertElement() {
        Em.run.scheduleOnce('afterRender', this, this._zcSwitchRender);
        Em.run.once(this, 'didInsertElement');
    }
});
```
```js
/* After - runloop usage */
import Component from '@ember/component';
import emberUtils from 'cide/utils/emberutils';
export default Component.extend({
    didInsertElement() {
        emberUtils.scheduleOnce('afterRender', this, this._zcSwitchRender);
        emberUtils.once(this, 'didInsertElement');
    }
});
```

`Ember Observer - usage`
```js
/* Before - observer usage*/
export default Em.Controller.extend({
    searchFiles: function() {
        console.log("Hello World");
    }.observes('fileSearchinput')
});
```
```js
/* After - observer usage */
import Controller from '@ember/controller';
import { observer } from '@ember/object';
export default Controller.extend({
    searchFiles: observer("fileSearchinput", function() {
        console.log("Hello World");
    })
});
```

`Ember Computed(property) - usage`
```js
/* Before - Computed(property) usage*/
export default Em.Controller.extend({
    currentUserFullName: function() {
		return this.get('currentUser.accounts.fullname');
	}.property('currentUserFullName')
});
```
```js
/* After - computed(property) usage */
import Controller from '@ember/controller';
import { computed } from '@ember/object';
export default Controller.extend({
    currentUserEmail: computed("currentUserEmail", function() {
        return this.get('currentUser.accounts.email');
    })
});
```

`Ember init function - usage`
```js
/* Before - init function usage */
export default Em.Controller.extend({
    initModule: function() {
    }.on('init')
});
```
```js
/* After - init function usage */
import Controller from '@ember/controller';
import { on } from '@ember/object/evented';
export default Controller.extend({
    initModule: on("init", function() {
    })
});
```

# Few Struggle while migration

`Error1: Error: Assertion Failed: You must use set() to set the `to` property (of [object Object]) to "68"`

`Ember getter setter Usage`
```js
/* Before - Ember getter setter Usage*/
export default Em.Controller.extend({
    setComplexityInfo: function() {
        let complexity_distribution = self.get('complexity_distribution');
        complexity_distribution.high.count = 0;
        complexity_distribution.v_high.count = 0;
        self.set("complexity_distribution", complexity_distribution);
    }
});
```
```js
/* After - Ember getter setter Usage*/
import Controller from '@ember/controller';
import emberUtils from 'cide/utils/emberutils';
export default Controller.extend({
    setComplexityInfo: function() {
        let complexity_distribution = self.get('complexity_distribution');
        emberUtils.set(complexity_distribution, "high.count", 0);
        emberUtils.set(complexity_distribution, "v_high.count", 0);
    }
});
```

`Error2: Error: Assertion Failed: You modified [property] twice in a single render`


# Deprecations - 2.3.0 to 3.3.0 (Our App Usage)
* [render helper with block](https://www.emberjs.com/deprecations/v2.x/#toc_render-helper-with-block) - **Not Used**
* [Ember.Component#didInitAttrs](https://www.emberjs.com/deprecations/v2.x/#toc_ember-component-didinitattrs) - **Not Used**
* [Legacy support addons](https://www.emberjs.com/deprecations/v2.x/#toc_legacy-support-addons) - **Used (Now removed)**
* [Model param in render helper](https://www.emberjs.com/deprecations/v2.x/#toc_model-param-in-code-render-code-helper) - **Not Used**
* [Ember.Backburner](https://www.emberjs.com/deprecations/v2.x/#toc_ember-metal-ember-backburner) - **Not Used**
* [Ember.Binding](https://www.emberjs.com/deprecations/v2.x/#toc_ember-binding) - **Not Used**
* [Array Contains function](https://www.emberjs.com/deprecations/v2.x/#toc_ember-runtime-enumerable-contains) - **Not Used (very Useful)**
* [Array without function](https://www.emberjs.com/deprecations/v2.x/#toc_ember-runtime-enumerable-contains) - **Used (Safe)**
* [Use Ember.String.htmlSafe over Ember.Handlebars.SafeString](https://www.emberjs.com/deprecations/v2.x/#toc_use-ember-string-htmlsafe-over-ember-handlebars-safestring) - **Code Changed**
* [{{render helper](https://www.emberjs.com/deprecations/v2.x/#toc_ember-template-compiler-deprecate-render) - **Not Used**
* [renderToElement](https://www.emberjs.com/deprecations/v2.x/#toc_code-rendertoelement-code) - **Not Used**
* [Rendering into a {{render}} helper that resolves to an {{outlet}}](https://www.emberjs.com/deprecations/v2.x/#toc_rendering-into-a-render-helper-that-resolves-to-an-outlet) - **Not Used(No Idea)**
* [Arguments in Component Lifecycle Hooks](https://www.emberjs.com/deprecations/v2.x/#toc_arguments-in-component-lifecycle-hooks) - **Not Used (No Idea)**
* [Migrating from _lookupFactory to factoryFor](https://www.emberjs.com/deprecations/v2.x/#toc_migrating-from-_lookupfactory-to-factoryfor) - **Not Used**
* [Ember.Router.router renamed to Ember.Router._routerMicrolib](https://www.emberjs.com/deprecations/v2.x/#toc_ember-router-router-renamed-to-ember-router-_routermicrolib) - **Not Used**
* [targetObject - Property on component](https://www.emberjs.com/deprecations/v2.x/#toc_ember-router-router-renamed-to-ember-router-_routermicrolib) - **Not Used**
* [Getting the @each property - Array.get("@each")](https://deprecations-app-prod.herokuapp.com/deprecations/v3.x/#toc_getting-the-each-property) - **Previously Used.Now Removed Usage**
* [Use notifyPropertyChange instead of propertyWillChange and propertyDidChange](https://deprecations-app-prod.herokuapp.com/deprecations/v3.x/#toc_use-notifypropertychange-instead-of-propertywillchange-and-propertydidchange) - **Not Used**
* [Use ember-copy addon instead of copy method and Copyable mixin.](https://deprecations-app-prod.herokuapp.com/deprecations/v3.x/#toc_ember-runtime-deprecate-copy-copyable) - **Previously Used.Now Removed Usage**
* [Use native events instead of jQuery.Event](https://deprecations-app-prod.herokuapp.com/deprecations/v3.x/#toc_jquery-event) - **Usage in ide-terminal.js (check it)** - Until 4.0.0
* [Use closure actions instead of `sendAction`](https://deprecations-app-prod.herokuapp.com/deprecations/v3.x/#toc_ember-component-send-action) - **Previously Used.Now Removed Usage**

# Deprecations Added in Upcoming Features
* [Ember.String namespace](https://deprecations-app-prod.herokuapp.com/deprecations/v3.x/#toc_ember-string-namespace) - **Future Use Careful**

# Initial Level Migration Steps:

* `REMOVE - jsapps/<app>/.bowerrc`
* `ADD - jsapps/<app>/.eslintignore`
* `ADD - jsapps/<app>/.eslintrc.js`
* `MODIFY -  jsapps/<app>/app/app.js`
* `MODIFY -  jsapps/<app>/app/index.html`
* `MODIFY - jsapps/<app>/app/router.js`
* `REMOVE - jsapps/<app>/bower.json`
* `REMOVE - jsapps/<app>/broccoli-tasks/ember-app.js`
* `MODIFY - jsapps/<app>/config/environment.js`
* `ADD - jsapps/<app>/config/targets.js`
* `MODIFY - jsapps/<app>/ember-cli-build.js`
* `MODIFY - jsapps/<app>/package.json`
* `ADD - jsapps/<app>/testem.js`
* `REMOVE - jsapps/<app>/testem.json`
* `REMOVE - jsapps/<app>/tests/.jshintrc`
* `REPLACE test FOLDER`

    REFER [Initial Level Migration Changes](https://git.csez.zohocorpin.com/cloudide/cide/commit/bbac30e675ff5f1157de1ebf8a4b881459b3e543#5df1e9ce41bdb0857b14f91b3297e16490392cc1)

### USAGE
* `grep -r "Em\." . | grep -v "vendor"`
