## Migration plan MultiselectComboBox to LitElement
This page describes the motivation, approach, questions and steps to migrate the `<multiselect-combo-box>` to LitElement.  

### Motivation
[LitElement](https://lit-element.polymer-project.org/) is the future of WebComponents. The MultiselectComboBox is an important extension to the Vaadin set of components. All Vaadin components are all rewritten to be based on LitElement. 

### Current design
Observations about the current Polymer 3 implementation of MultiselectComboBox:
- The `<multiselect-combo-box>` is based on `<vaadin-combo-box-light>` with the `<multiselect-combo-box-input>` rendered inside of it.
- The `<multiselect-combo-box-input>` is based on a `<vaadin-text-field>`.
- This `<vaadin-text-field>` uses two suffix slots, for the `clearButton` and for the `toggleButton`. 
- `class MultiselectComboBoxInput extends ThemePropertyMixin(ThemableMixin(MultiselectComboBoxMixin(PolymerElement)))`
- `class MultiselectComboBox extends ControlStateMixin(ThemePropertyMixin(ThemableMixin(MultiselectComboBoxMixin(mixinBehaviors([IronResizableBehavior], PolymerElement)))))`

### Notable differences between Polymer 3 and LitElement
- Databinding in Polymer 3 can be two-way. In LitElement, data binding is always one-way (parent to child). To share data from a child element to its parent, fire an event and capture the relevant data in the detail property.
    - Polymer 3: Double-curly brackets (`{{ }}`) support both upward and downward data flow.
    - Polymer 3: Double square brackets (`[[ ]]`) are one-way, and support only downward data flow.
- _other...?_

### Polymer 3 constructs that need to be translated/reworked:
Found in `multiselect-combo-box.js` and `multiselect-combo-box-input.js`:
- Styling: `<style>...</style>` -> To separate CSS files
- Use of label element: `<label part="label">[[label]]</label>` ->  
- Data binding: `[[label]]` -> `${label}`
- Unclear what this is: `<div part="readonly-container" hidden$="[[!readonly]]"> [[_getReadonlyValue(selectedItems, itemLabelPath, compactMode, readonlyValueSeparator)]] </div>` ->
- Boolean attribute(s): `hidden$="[[!readonly]]"` -> `?hidden="${...}"` 
- Lifecycle method: `ready() {}` method -> 
- Defining properties: `static get properties()` -> Individual properties in the class annotated with `@property`
- Defining observers: `static get observers() {}` -> 
- Setting the template: `this.$.comboBox._itemTemplate = template;` -> 
- Getting the child inputElement: `get inputElement() { return this.$.input; }` -> 
- Catching events: `_customOverlaySelectedItemChanged(event)` -> Probably something with e: CustomEvent
- Checking if a function exists: `_hasDataProvider() { return this.$.comboBox.dataProvider && typeof this.$.comboBox.dataProvider === 'function'; }` -> 
- Marking as customElement: `customElements.define(MultiselectComboBox.is, MultiselectComboBox);` -> `@customElement('multiselect-combo-box')`

### Questions / open points
- Removal of bower / use of pnpm
- Automatic testing of the component
- Which version of the Vaadin components will we rely on...?
  - How do we ensure upgrade...?

### Migration approach
_To be confirmed._
1) Migrate `src/multiselect-combo-box-input.js`
1) Migrate `src/multiselect-combo-box-mixin.js`
1) Migrate `src/multiselect-combo-box.js`
1) Create CSS files for `theme/lumo/*.js`

Ensure that `demo/index.html` keeps working after every step.

### References
- [Polymer 3 Homepage](https://polymer-library.polymer-project.org/3.0/docs/devguide/properties)
- [Polymer 3 Lifecycle callbacks](https://polymer-library.polymer-project.org/1.0/docs/devguide/registering-elements)  
- [Polymer 3 - Curly braces vs Square brackets](https://stackoverflow.com/questions/51947790/polymer-data-bindings-curly-vs-square-brackets)
- [LitElement Homepage](https://lit-element.polymer-project.org/)
- [LitElement Lifecycle callbacks](https://lit-element.polymer-project.org/guide/lifecycle)  
- [LitElement - Bind properties to templated elements](https://lit-element.polymer-project.org/guide/templates#bind-properties-to-templated-elements)


