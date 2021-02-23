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

Let's look at one of our bigger customer projects built on Vue 2. It uses the following Vue-related libraries and tools:

As of 2021-02-23:

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

So in general the UI component libraries are lagging behind. If a project needs such a library, it is probably still too early to adopt Vue 3.

# Improved Typescript support

# New Composition API

# Migration from Vue 2 to Vue 3

# Resources

# Summary