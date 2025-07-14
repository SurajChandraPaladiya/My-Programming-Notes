# JavaScript Events

## 1. What are Events in JavaScript?

- **Definition**: Events are actions or occurrences in the browser, such as user interactions (e.g., clicking, typing) or system actions (e.g., page loading). JavaScript detects and responds to these to create interactive web applications.
- **How Events Work**:
  - Triggered by user actions (e.g., `click`, `keypress`) or system events (e.g., `load`, `resize`).
  - JavaScript listens for events and executes a function (event handler) when they occur.
- **Common Events**:
  - **Mouse**: `click`, `dblclick`, `mouseover`, `mouseout`
  - **Keyboard**: `keydown`, `keyup`, `keypress`
  - **Form**: `submit`, `change`, `focus`, `blur`
  - **Window**: `load`, `resize`, `scroll`
- **Key Concept**: Events enable interactivity by linking user actions to JavaScript code.

**Example**:
```javascript
// Listening for a button click
const button = document.querySelector('button');
button.onclick = () => alert('Button clicked!');
```

## 2. Best Ways to Apply Events

There are three methods to attach event listeners. The **recommended** method is `addEventListener`.

### a. Using `addEventListener` (Best Practice)
- **Description**: Attaches an event listener without overwriting existing handlers. Most flexible and modern.
- **Syntax**:
  ```javascript
  element.addEventListener(event, handler, [options]);
  ```
  - `event`: Event type (e.g., `'click'`).
  - `handler`: Function to execute.
  - `options`: Optional (e.g., `{ capture: true }` or `{ once: true }`).
- **Advantages**:
  - Supports multiple listeners for the same event.
  - Easy to remove with `removeEventListener`.
  - Enables event delegation and phase control.
- **Example**:
  ```javascript
  const button = document.querySelector('button');
  button.addEventListener('click', () => {
    console.log('Button was clicked!');
  });
  ```

### b. Inline Event Handlers (Not Recommended)
- **Description**: Adding handlers in HTML using attributes like `onclick`.
- **Example**:
  ```html
  <button onclick="handleClick()">Click me</button>
  <script>
    function handleClick() {
      console.log('Button clicked!');
    }
  </script>
  ```
- **Disadvantages**:
  - Mixes HTML and JavaScript, reducing maintainability.
  - Limited to one handler per event.
  - No support for advanced features.

### c. Using Event Properties (e.g., `element.onclick`)
- **Description**: Assigning a function to an element’s event property.
- **Example**:
  ```javascript
  const button = document.querySelector('button');
  button.onclick = () => console.log('Button clicked!');
  ```
- **Disadvantages**:
  - Overwrites existing handlers.
  - Less flexible than `addEventListener`.

### Best Practice:
- Use `addEventListener` for clean code, multiple listeners, and advanced features.
- Avoid inline handlers and event properties unless for simple scripts.

## 3. Event Bubbling and Capturing

Events propagate through the DOM in two phases: **capturing** (top-down) and **bubbling** (bottom-up).

### a. Event Bubbling
- **Definition**: Event triggers on the target element, then bubbles up to parent elements, up to `document`.
- **Default Behavior**: Most events bubble.
- **Example**:
  ```html
  <div id="parent">
    <button id="child">Click me</button>
  </div>
  <script>
    document.getElementById('parent').addEventListener('click', () => {
      console.log('Parent clicked');
    });
    document.getElementById('child').addEventListener('click', () => {
      console.log('Child clicked');
    });
  </script>
  ```
  - **Output** (on button click):
    ```
    Child clicked
    Parent clicked
    ```
- **Stopping Bubbling**:
  - Use `event.stopPropagation()` to prevent bubbling.
  - **Example**:
    ```javascript
    document.getElementById('child').addEventListener('click', (event) => {
      event.stopPropagation();
      console.log('Child clicked, bubbling stopped');
    });
    ```

### b. Event Capturing
- **Definition**: Event starts at `document` and moves down to the target element.
- **Enable**: Set `addEventListener`’s third parameter to `{ capture: true }`.
- **Example**:
  ```javascript
  document.getElementById('parent').addEventListener('click', () => {
    console.log('Parent captured');
  }, { capture: true });
  document.getElementById('child').addEventListener('click', () => {
    console.log('Child clicked');
  });
  ```
  - **Output** (on button click):
    ```
    Parent captured
    Child clicked
    ```

### c. Event Phases
- **Phases**:
  1. **Capturing**: From `document` to target.
  2. **Target**: On the target element.
  3. **Bubbling**: From target to `document`.
- **Check Phase**: Use `event.eventPhase` (`1` = capturing, `2` = target, `3` = bubbling).

## 4. Other Related Topics

### a. Event Delegation
- **Definition**: Attach a single listener to a parent element to handle events for multiple children, using bubbling.
- **Benefits**:
  - Reduces number of listeners, improving performance.
  - Handles dynamically added elements.
- **Example**:
  ```html
  <ul id="list">
    <li>Item 1</li>
    <li>Item 2</li>
  </ul>
  <script>
    document.getElementById('list').addEventListener('click', (event) => {
      if (event.target.tagName === 'LI') {
        console.log(`Clicked: ${event.target.textContent}`);
      }
    });
  </script>
  ```

### b. Preventing Default Behavior
- **Definition**: Prevent browser’s default action (e.g., form submission) using `event.preventDefault()`.
- **Example**:
  ```html
  <form id="myForm">
    <input type="submit" value="Submit">
  </form>
  <script>
    document.getElementById('myForm').addEventListener('submit', (event) => {
      event.preventDefault();
      console.log('Form submission prevented');
    });
  </script>
  ```

### c. Event Object
- **Definition**: The `event` object passed to handlers contains event details.
- **Common Properties**:
  - `event.target`: Element that triggered the event.
  - `event.type`: Event type (e.g., `'click'`).
  - `event.currentTarget`: Element handling the event.
  - `event.clientX`, `event.clientY`: Mouse coordinates.
- **Note**: Detailed event object notes can be provided later.

### d. Custom Events
- **Definition**: Create and dispatch custom events using `CustomEvent`.
- **Example**:
  ```javascript
  const myEvent = new CustomEvent('myCustomEvent', { detail: { message: 'Hello!' } });
  document.addEventListener('myCustomEvent', (event) => {
    console.log(event.detail.message); // Hello!
  });
  document.dispatchEvent(myEvent);
  ```

### e. Event Listener Removal
- **Description**: Remove listeners with `removeEventListener` using the same handler.
- **Example**:
  ```javascript
  const handler = () => console.log('Clicked!');
  button.addEventListener('click', handler);
  button.removeEventListener('click', handler);
  ```

### f. Passive Event Listeners
- **Description**: Optimize performance for events like `scroll` by marking them as passive (no `preventDefault`).
- **Example**:
  ```javascript
  window.addEventListener('scroll', () => {
    console.log('Scrolling...');
  }, { passive: true });
  ```

## 5. Key Tips for Revision

- **Event Flow**: Memorize capturing, target, and bubbling phases.
- **Use `addEventListener`**: Build habit of using it for all event handling.
- **Event Delegation**: Practice handling events on parent elements.
- **Prevent Default**: Test with forms and links.
- **Event Object**: Log `event` in different scenarios to explore properties.

## 6. Practice Example

```html
<div id="container">
  <button id="btn">Click me!</button>
</div>
<script>
  const container = document.getElementById('container');
  const button = document.getElementById('btn');

  // Bubbling
  container.addEventListener('click', () => console.log('Container clicked'));
  button.addEventListener('click', (e) => {
    console.log('Button clicked');
    // e.stopPropagation(); // Uncomment to stop bubbling
  });

  // Capturing
  container.addEventListener('click', () => console.log('Container captured'), { capture: true });
</script>
```

- **Task**: Run the code, observe output, and experiment (e.g., add `stopPropagation`).