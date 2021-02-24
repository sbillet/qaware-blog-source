// Title: Is Vue 3 ready for enterprise use?

# Intro

At QAware, we often build "enterprise" applications for our customers. Big dev teams (5-15) develop and maintain them over a long time (years). Not surprisingly, these applications get quite big and complex over time. Therefore, we put high value on scalability when making architecture and technology choices.

For web applications, Angular is usually our default frontend framework choice because of its native TypeScript support - a big factor for scalability - and wide adoption in enterprises. We consider Vue 2 a contender - also for enterprise applications - with some (subjective) benefits over Angular like ease of use (concepts, tooling, documentation), but also some question marks around scalability (TypeScript support, code reuse).

Vue 3 was released in September 2020 and promises the following main [improvements](https://v3.vuejs.org/guide/migration/introduction.html#overview) over Vue 2:
* Performance: Same small size as Vue 2 (20 KB gzipped at runtime), faster rendering because of virtual DoM optimizations
* Composition API: Alternative more scalable syntax for writing components
* Enhanced TypeScript Support

At first glance, Vue 3 seems to address many of the concerns we have with Vue 2. Is Vue now a perfect match for enterprise web applications? And is it mature enough to use it in existing and new projects?

# State of the ecosystem

Vue, like other frameworks, greatly depends on complementing libraries, both  first party and third party. When considering to use Vue 3 in a project, we must check if all the dependencies also support Vue 3.

Let's look at one of our recent customer projects built on Vue 2 that I was involved with. It uses the following Vue-related libraries and tools whose Vue 3 support must be checked when considering a migration (data as of 2021-02-23):

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
| Tool | IntelliJ Vue plugin | IDE  | Yes |
| Tool | SonarCloud | Code analysis  | unclear |

As expected, most official libraries and tools have stable support (or close to it) by now. Third-party library support varies greatly. 

Notably, Vuetify, used as UI component library in the project shows very little progress - a migration blocker! 

How about other popular UI component libraries besides Vuetify?
* Quasar: Beta since 2021-02-03 (2.0.0)
* Element UI: Beta since 2020-12-23 ("Element Plus")
* BootstrapVue: WIP, not even alpha yet
* Buefy: WIP, not even alpha yet

So in general the UI component libraries are lagging far behind. If a project needs such a library, it is probably still too early to adopt Vue 3. I expect the majority of popular UI component libraries will need at least until Summer 2021, maybe even longer, to reach stable Vue 3 support.

# Improved TypeScript support

TypeScript is already supported in Vue 2, but as a developer it feels really rough around the edges. Vue 3 - being completely built in TypeScript itself - promises to lift TypeScript support to first-class citizen.

TypeScript support is all about developer experience, so I created and played around with a demo project using similar tools and libs to the above mentioned Vue 2 project.

## Setting up a demo project

Install the Vue CLI:

`npm install -g @vue/cli`

Check the installed version (tells me `4.5.11`).

`vue --version`

Create a new project:

`vue create vue3-demo`

Choose preferred options in project creation wizard:
* Features: Choose Vue version, Babel, TypeScript, Router, Vuex, Linter/Formatter, Unit Testing
* Version: 3.x (Preview)
* Class-style component syntax: no
* Babel alongside TypeScript: yes
* History mode for router: yes
* Linter/formatter: ESLint + Prettier
* Additional lint features: Lint on save
* Unit testing: Jest
* Config files: dedicated files

Wait a few minutes until the project is created. Mental note: The Vue-CLI still regards Vue 3 as "Preview", 5 months after release - acknowledging the ecosystem is not mature enough yet?

Open the project in my favorite IDE IntelliJ (~ WebStorm), version 2020.03.02 with Vue.js-Plugin 203.7148.50.

Install the application and serve it locally.

`npm install`

`npm run serve`

_Failed to compile: Syntax Error: Error: No ESLint configuration found._

Manually add missing `.eslintrc.js` with a minimal configuration:

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

Because Vue 3 is internally built in TypeScript, we can expect the Core API to have solid typings, right?

### Component declaration, props

Checking types of props/data in templates works as expected:

![](/images/vue3-eslint-template-type-check.png)

Type assertion + import leads to a false positive unused warning. This could prove very annoying in a real project. With a bit of luck it can be prevented by updating some dependencies or tweaking the ESLint/TS configuration, but I didn't investigate further:

![](/images/vue3-eslint-false-positive.png)

Vue 3 code completion for core APIs like prop definitions still lacks API comments, but at least shows types and available attributes:

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

Vuex is the official Redux-like state management library for Vue. Vuex 3.x is compatible with Vue 2 and Vuex 4.x is compatible with Vue 3 (yes, confusing).

One of the biggest pains of Vuex 3 + TypeScript is the typing, or rather lack of typing. The standard way to interact with the store from components is referencing the Store variables or functions by "magic strings". That is really bad, even for untyped JavaScript...

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

My impression: Very disappointing. At least the creators have realized it and stated they make improving it a priority in the next major version (whenever it will come).

Until then, everyone who wants to use Vuex in a typesafe way needs to hack his/her own abstraction layer around the Store, often involving obscure TypeScript magic. We have done so for our customer project and so have others (google "Vuex TypeScript").

# New Composition API



# Summary