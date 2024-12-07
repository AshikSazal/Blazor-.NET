# Blazor Component Lifecycle

In Blazor, a component goes through several lifecycle events. These events represent different phases during the component's lifecycle, allowing you to hook into and control the behavior of your component. Here's an overview of the lifecycle flow:

## Lifecycle Events

### 1. **Construct**
- **Event**: `Constructor()`
- This is the initial step when an instance of the component is created. The constructor is used to inject any dependencies into the component.

### 2. **Instantiation**
- **Event**: Happens immediately after the constructor.
- At this point, the component is instantiated, but the parameters haven't been set yet. Dependencies are injected, but the component's properties or state haven't been populated.

### 3. **Set Parameters (Before Setting Parameters)**
- **Event**: `SetParametersAsync()`
- This method is called when the component is provided with parameters, either from its parent component or route parameters.
- It happens **before** the parameters are applied to the component, and you can override this method to modify the incoming parameters if needed.

### 4. **Initialize**
- **Event**: `OnInitialized()` or `OnInitializedAsync()`
- This is called when the component is initialized. It's typically used to perform one-time setup tasks, such as fetching data from a server or initializing variables.
- You can implement the synchronous `OnInitialized()` or the asynchronous version, `OnInitializedAsync()`, if you need to perform async operations.

### 5. **Receive Parameters**
- **Event**: `OnParametersSet()` or `OnParametersSetAsync()`
- This method is called after the parameters are set and whenever they change. It's useful when you need to react to changes in the parameters passed to the component.

### 6. **Render**
- **Event**: `BuildRenderTree()`
- This is the phase where Blazor renders the component's UI. During this phase, the component’s state and properties are used to generate HTML markup.
- You don't usually need to override this, but it's important because this is where the component’s markup is generated.

- **Event**: `OnAfterRender()` or `OnAfterRenderAsync()`
- This is called after the component has rendered. If you need to perform any additional tasks after rendering (like calling JavaScript functions or interacting with DOM), you can implement `OnAfterRender` or its async counterpart `OnAfterRenderAsync()`.

### 7. **Dispose**
- **Event**: `Dispose()`
- This method is called when the component is removed from the UI or is disposed of. It is used for cleanup tasks like releasing resources, cancelling timers, or unsubscribing from events.

### 8. **Destruct**
- This occurs after the component is disposed of. In Blazor, this is not an explicit event like `Dispose()`, but it's when the component is no longer part of the UI or lifecycle.

## Component Lifecycle Flow

Here’s the typical lifecycle flow:

```plaintext
Constructor() 
    ↓
SetParametersAsync()
    ↓
OnInitialized() or OnInitializedAsync()
    ↓
OnParametersSet() or OnParametersSetAsync()
    ↓
BuildRenderTree() (Renders UI)
    ↓
OnAfterRender() or OnAfterRenderAsync()
    ↓
Dispose()
    ↓
Destruction (Component removed)

Apologies for the misunderstanding! Here's the entire explanation of the Blazor component lifecycle after user interaction in a proper `README.md` format:

```markdown
# Blazor Component Lifecycle After User Interaction

In Blazor, when a user interacts with a component (e.g., clicking a button or changing input), several lifecycle methods are triggered to handle the re-rendering, parameter changes, and updates. Below is an explanation of the lifecycle methods that occur after user interaction:

## 1. **ShouldRender**

- **When It Happens**:  
  `ShouldRender` is called **before** the component is re-rendered. It occurs after a state change or user interaction (e.g., button click, form submission, etc.).

- **How It Works**:  
  This method determines whether the component should be re-rendered. By default, it returns `true`, but you can override it to return `false` if re-rendering is unnecessary.

- **When to Use**:
  You can override `ShouldRender` to optimize performance by skipping re-renders if the UI has not changed or if certain state conditions haven’t changed.

  ```csharp
  protected override bool ShouldRender()
  {
      // Only re-render if certain condition is met
      return myStateHasChanged;
  }
  ```

## 2. **SetParametersAsync (Before Setting Parameters)**

- **When It Happens**:  
  `SetParametersAsync` is called **before** the component’s parameters are applied. It occurs when a parent component passes new parameters to a child component or when route parameters change.

- **How It Works**:  
  This method gives you a chance to inspect or modify the incoming parameters before they are set on the component. It's typically used when the parent component changes the parameters passed to the child component after user interaction.

- **When to Use**:  
  Override `SetParametersAsync` if you need to modify or validate the incoming parameters before they are applied to the component.

  ```csharp
  public override Task SetParametersAsync(ParameterView parameters)
  {
      // Modify or validate parameters before they are applied
      return base.SetParametersAsync(parameters);
  }
  ```

## 3. **OnParametersSetAsync**

- **When It Happens**:  
  `OnParametersSetAsync` is called after the parameters have been set on the component and any changes to parameters have been applied. This happens after `SetParametersAsync` and is called each time the component's parameters change, including after user interactions.

- **How It Works**:  
  This method is typically used to perform operations based on changes to the parameters, such as fetching new data, setting internal state, or triggering UI updates.

- **When to Use**:  
  Override `OnParametersSetAsync` when you need to perform logic or actions after the parameters have been set or changed, such as asynchronous operations like loading data or modifying the component’s state.

  ```csharp
  protected override async Task OnParametersSetAsync()
  {
      // Perform actions after parameters are set, like fetching data
      await myDataService.LoadDataAsync();
  }
  ```

## 4. **OnAfterRenderAsync**

- **When It Happens**:  
  `OnAfterRenderAsync` is called **after** the component has rendered or re-rendered. It occurs once the component’s HTML is generated and added to the DOM. This method is invoked after every render, including the initial render and any subsequent re-renders due to user interaction.

- **How It Works**:  
  This method allows you to execute any logic that requires interaction with the DOM or external resources, such as calling JavaScript functions, initializing third-party libraries, or updating the DOM.

- **When to Use**:  
  Override `OnAfterRenderAsync` if you need to perform tasks like JavaScript interop, DOM manipulation, or trigger actions that depend on the component’s rendering.

  ```csharp
  protected override async Task OnAfterRenderAsync(bool firstRender)
  {
      if (firstRender)
      {
          // Perform actions only on the first render (e.g., initialize JS)
          await JS.InvokeVoidAsync("initializeComponent", elementRef);
      }
  }
  ```

## Lifecycle Flow After User Interaction

When a user interacts with the component, such as clicking a button or modifying a form field, the following sequence of lifecycle methods occurs:

1. **ShouldRender**:  
   Called to check if the component should re-render. If it returns `true`, the rendering process continues.

2. **SetParametersAsync**:  
   Called **before** the parameters are applied. If parameters are passed from the parent, you can modify them here.

3. **OnParametersSetAsync**:  
   Called after the parameters are applied, allowing you to respond to any changes in the parameters or perform other logic.

4. **OnAfterRenderAsync**:  
   Called after the component is rendered or re-rendered. This method is useful for tasks that involve the DOM, such as JavaScript interop or initializing third-party libraries.

## Example Scenario: Counter Component

Imagine you have a simple counter component that updates when a user clicks a button. The lifecycle methods would work as follows:

- **User Clicks "Increment" Button**:
  1. **ShouldRender**: The component checks whether it needs to re-render. This will likely return `true` because the counter value changed.
  2. **SetParametersAsync**: If the parent component passes an initial counter value, this method allows you to inspect and modify it.
  3. **OnParametersSetAsync**: After the counter value is set, this method is invoked. If the counter is updated, you might perform actions like logging the new value or fetching related data.
  4. **OnAfterRenderAsync**: After the component is rendered, you might call JavaScript functions, like updating a chart that reflects the new counter value.

## Key Takeaways

- **`ShouldRender`** determines whether the component should be re-rendered and helps with performance optimization.
- **`SetParametersAsync`** is called before parameters are applied, allowing you to modify or validate incoming parameters.
- **`OnParametersSetAsync`** allows you to perform actions based on changes to the component's parameters after they are set.
- **`OnAfterRenderAsync`** is invoked after rendering and lets you interact with the rendered DOM or perform additional tasks that rely on the UI.

By understanding these lifecycle methods, you can effectively manage user interactions and optimize your Blazor components for better performance and interactivity.
```

### Explanation:
- The content is formatted with headers (`##`) to separate each lifecycle method.
- Code examples are provided in each section to show practical usage.
- The flow of lifecycle events after user interaction is explained step by step.
- An example scenario is included to help visualize how these lifecycle methods work in practice.
- This structure uses Markdown formatting to ensure the content is readable and easy to navigate.

You can directly copy this into your `README.md` file, and it will display clearly formatted documentation for developers using your project.

There is a problem in OnParamtersSet() function because this file will render then OnParametersSet() function will call even CityName doesn't changed yet. So the solution is used SetParamtersAsync() function which has written in above.

When does a component render/re-render?
I. When component is created for the first time
2. When events are triggered (has to be UI events, including EventCallback)
3. When component parameter value changes
4. When StateHasChange is called