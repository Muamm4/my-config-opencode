# Filament v5 Forms & Schemas

## Core Components
- **Text**: `TextInput`, `Textarea`, `RichEditor`, `MarkdownEditor`, `CodeEditor`
- **Selection**: `Select`, `MultiSelect`, `Checkbox`, `Toggle`, `Radio`, `CheckboxList`, `ToggleButtons`
- **Date/Time**: `DatePicker`, `DateTimePicker`
- **Files**: `FileUpload`, `ImageUpload`
- **Special**: `Slider`, `TagsInput`, `KeyValue`, `Builder`, `Repeater`

## Layout Components
- **Grid**: `Grid::make(['default' => 1, 'md' => 2])->schema([...])`
- **Section**: `Section::make('Title')->description('...')->schema([...])->collapsible()`
- **Tabs**: `Tabs::make()->tabs([ Tab::make('Label')->schema([...]) ])`
- **Wizard**: `Wizard::make()->steps([ Step::make('Label')->schema([...]) ])`
- **Others**: `Split`, `Fieldset`, `Callout`, `EmptyState`

## State Management & Reactivity
- **Accessing State**: `$get('field_name')`
- **Setting State**: `$set('field_name', 'value')`
- **Reactivity**: `live()` makes a field reactive. Use `debounce(ms)` to limit requests.
- **Callbacks**: `afterStateUpdated(fn ($state, $set, $get) => ...)`
- **Casting**: `castStateUsing()` and `dehydrateStateUsing()`.

## Validation
- **Standard**: `required()`, `email()`, `numeric()`, `maxLength(255)`, `unique('table', 'column')`
- **Custom**: `rules([ fn() => ... ])` or `Rule::unique(...)`
- **Conditional**: `requiredIf(fn ($get) => $get('other_field') === 'value')`
