// Title: Is Vue 3 ready for enterprise use?

# Intro

[Vue.js](https://vuejs.org/) is one of the most popular JavaScript frontend frameworks. The first public release was in 2014. The major version 2 was released in 2016 and widely adopted. After around 4 years, in September 2020 the major version 3 (dubbed [vue-next](https://github.com/vuejs/vue-next)) was released.

At QAware, we often build enterprise web applications for our customers. As these tend to get quite big and complex over time we value technologies that enable us to design systems aiming for good quality, scalability and performance.

In recent years, Angular was usually the default framework choice because of its native TypeScript support and proven track-record for enterprise-grade web applications. Vue 2 was considered a valid alternative with some (subjective) benefits over Angular like ease of use (concepts, tooling, documentation), but also some drawbacks like worse TypeScript support and questions marks around scalability for big applications.

Vue 3 promises the following main [improvements](https://v3.vuejs.org/guide/migration/introduction.html#overview) over Vue 2:
* Performance: Same small size as Vue 2 (20 KB gzipped at runtime), faster rendering because of virtual DoM optimizations
* Composition API: Alternative more scalable syntax for writing components
* Enhanced TypeScript Support

At first glance, v3 seems to address many of the concerns we had. Is Vue now a perfect match for enterprise web applications? Is v3 mature enough to migrate existing projects or start new ones?

# State of the ecosystem

Vue, like other frameworks, greatly depends on complementing libraries (both first party and third party). To adopt Vue 3 for a project, all the required libraries must also support Vue 3.

Let's look at one of our bigger customer projects built on Vue 2. It uses the following Vue-related libraries and tools (as of 2021-02-23):

| Category | Library/Tool | Description | Vue 3 support |
| ------------- |:-------------:| -----:| -----:|
| Main dep. | vue-router | Routing | Yes - Stable since 2020-12-07 (4.0.0) |
| Main dep. | vuex | State management | Yes - Stable since 2021-02-02 (4.0.0) |
| Main dep. | vuetify | Layout and UI components | **No - WIP, not even alpha yet** |
| Main dep. | vue-i18n | Internationalization | Yes - RC since 2021-01-06 (9.0.0) |
| Main dep. | ckeditor4-vue | WYSIWYG-HTML-Editor  | [unclear](https://github.com/ckeditor/ckeditor4-vue/issues/6) |
| Main dep. | qrcode.vue | QR-Codes  | Yes - Stable since 2020-12-30 (3.0.0) |
| Main dep. | vue-matomo | Matomo-Tracking integration  | Yes ([Demo](https://github.com/AmazingDreams/vue-matomo/tree/master/demo/vue-3)) |
| Main dep. | vue-pdf | PDF-Viewer  | **No - [not planned](https://github.com/FranckFreiburger/vue-pdf/issues/259)** |
| Dev dep. | eslint-plugin-vue | Linter  | Yes - Stable since 2020-09-30 (7.0.0) |
| Dev dep. | vue-test-utils | Test-Framework  | Yes - RC since 2021-01-26 (2.0.0) |
| Dev dep. | @testing-library/vue | Test-Framework  | **No - WIP since 2020-10-31 (6.0.0)** |
| Tool | @vue/cli | CLI  | Yes - Stable since 2020-07-24 (4.5.0)  |
| Tool | Vue Devtools | Browser Devtools  | Yes - Beta since 2020-07-17 (6.0.0) |
| Tool | IntelliJ Vue plugin | IDE  | unclear |
| Tool | SonarCloud | Code analysis  | unclear |

As expected, most official libraries and tools have stable support (or close to it) by now. Third-party library support varies greatly. 

Notably, Vuetify, used as UI component library in the project shows very little progress - this is a migration blocker! 

How about other popular UI component libraries besides Vuetify?
* Quasar: Beta since 2021-02-03 (2.0.0)
* Element UI: Beta since 2020-12-23 ("Element Plus")
* BootstrapVue: WIP, not even alpha yet
* Buefy: WIP, not even alpha yet

So in general the UI component libraries are lagging far behind. If a project needs such a library, it is probably still too early to adopt Vue 3. I expect the majority of popular UI component libraries to need at least until Summer 2021, maybe even longer, to reach stable Vue 3 support.

# Improved TypeScript support

TypeScript was already supported in Vue 2, but as a developer you could feel that it was really rough around the edges. Vue 3 - being completely built in TypeScript itself - promises to lift TypeScript support to first-class citizen.

TypeScript support is all about developer experience, so I just tried to create and work with a demo project from scratch using the tools and libs am familiar from the above mentioned Vue 2 project.

## Setting up a demo project

Install the Vue CLI:

`npm install -g @vue/cli`

Check the installed version (tells me `4.5.11`).

`vue --version`

Create a new project:

`vue create vue3-demo`

In the project creation wizard, I chose the following options:
* Preset: Manually select.
* Features: Choose Vue version, Babel, TypeScript, Router, Vuex, Linter/Formatter, Unit Testing
* Version: 3.x (Preview)
* Class-style component syntax: no
* Babel alongside TypeScript: yes
* History mode for router: yes
* Linter/formatter: ESLint + Prettier
* Additional lint features: Lint on save
* Unit testing: Jest
* Config files: dedicated files

Wait a few minutes until the project is created. Mental note: The Vue-CLI still regards Vue 3 as "Preview", 5 months after release.

Open in my favorite IDE IntelliJ (~ WebStorm), version 2020.03.02 with Vue.js-Plugin 203.7148.50.

Install the application and serve it locally.

`npm install`

`npm run serve`

_Failed to compile: Syntax Error: Error: No ESLint configuration found._

Manually add `.eslintrc.js` with a minimal configuration:

```javascript
module.exports = {
  parserOptions: {
    parser: "@typescript-eslint/parser",
  },
  extends: [
    'plugin:vue/vue3-recommended',
  ]
}
```

Except for the ESlint fail, the project scaffolding works nicely.

## Core API typing

Because Vue 3 is internally built in TypeScript, we can expect the Core API to have solid typings.

### Component declaration, props

Checking types of props/data in templates works as expected:

![](/images/vue3-eslint-template-type-check.png)

Type assertion + import leads to a false positive unused warning. This could prove very annoying in a real project. With a bit of luck it can be prevented by updating some dependencies, but I didn't investigate further:

![](/images/vue3-eslint-false-positive.png)

Vue 3 code completion for core APIs like prop definitions still lacks API comments, but at least shows available attributes:

![](/images/vue3-vue3-code-completion.png)

Compare that to Vue 2 prop code completion, which was not helpful at all, because it showed random similar names from all over the codebase:

![](/images/vue3-vue2-code-completion.png)

Vue 3 prop type definition is still awkward requiring the obscure `PropType` helper:

![](/images/vue3-prop-type-def.png)

### Event handling

Emitting events ist very unsatisfactory in Vue 2 + TypeScript, because event names are basically strings and the payload can be anything. Example:

```typescript
// Component emitting the event (in script)
this.$emit("import-accounts", new BulkImportEvent(this.csvFile, replaceExisting));
```

```html
<!-- Component receiving the event (in template) -->
<v-tab-item key="registrations-roles">
    <the-registrations-roles-tab @import-accounts="importAccounts" />
</v-tab-item>
```

```typescript
// Component receiving the event (in script)
export default Vue.extend({
    // ...
    methods: {
        importAccounts(event: BulkImportEvent) {
            // ...
        }
    }
    // ...
});
```

Changing the event name or payload type is not detected as compile error.

Vue 3 improves event handling a lot by allowing to declare in the component which events can be emitted and then checking all emits:

![](/images/vue3-emit-typing.png)

However, the receiving component can still not catch the events in the HTML template in a typesafe way.

## Vuex store typing

Vuex is the official Redux-like state management library for Vue. Vuex 3.x is compatible with Vue 2 and Vuex 4.x is compatible with Vue 3.

One of the biggest pains of Vue 2 + TypeScript is the typing, or rather lack of typing. The standard way to interact with the store from components is referencing the Store variables or functions by "magic strings". That is really bad, even for untyped JavaScript...

```typescript
  // commit a mutation
  store.commit('increment');

  // dispatch an action
  store.dispatch('increment');
  
  // map getters to computed functions in a component
  export default Vue.extend({
    // ...
    computed: {
    ...mapGetters([
            'doneTodosCount',
            'anotherGetter',
            // ...
        ])
    }
    // ...
  });
```

What can we expect from Vuex 4.x? From the [maintainer's presentation](https://www.youtube.com/watch?v=ajGglyQQD0k) at the Vue 3 Launch event: _Slightly_ better TypeScript support.

![](/images/vue3-vuex4-ts-support.png)

_Complete_ TypeScript support (along with other major changes) is promised for Vuex 5.x:

![](/images/vue3-vuex5-ts-support.png)

However, Vuex 5.x is not even in beta yet...

So, how does TypeScript feel in the _slightly_ improved Vuex 4?

First thing to notice is that we now need to provide a shim file with custom typings for the store, or Vuex will not work at all with TypeScript (see [documentation](https://next.vuex.vuejs.org/guide/typescript-support.html)).

Store-Getters are still `any` typed, i.e. there is still the need for an additional ugly abstraction layer if you want to hide the untyped Getters from the Vue components:

![](/images/vue3-vuex4-typing-any-getter.png)

Even with a custom typing shim file, type inference still does not work as expected:

![](/images/vue3-vuex4-typing-inference-broken.png)

My impression: Very disappointing. At least the creators have realized that and stated they want to improve it in the next major version (whenever it will come).

# New Composition API



# Summary