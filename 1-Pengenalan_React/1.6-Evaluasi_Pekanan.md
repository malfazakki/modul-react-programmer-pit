# Take Home Test: React Todo Application

## Project Overview

Build a Todo List application using React that demonstrates your understanding of fundamental React concepts covered in the first week of the React Programmer course. The application should be functional, well-structured, and completed within 6 hours of work.

## Project Requirements

### Core Features (Must Implement)

1. **Add New Todo**
   - Input field to enter new todo text
   - Submit button or Enter key to add the todo
   - Clear input after adding

2. **Display Todo List**
   - Show all todos in a list format
   - Each todo should display its text and completion status
   - Use proper list rendering with unique keys

3. **Toggle Todo Completion**
   - Checkbox or clickable element to mark todo as complete/incomplete
   - Visual indication for completed todos (e.g., strikethrough text)

4. **Delete Todo**
   - Delete button for each todo item
   - Remove todo from the list when clicked

5. **Filter Todos**
   - Three filter options: All, Active, Completed
   - Filter buttons that update the displayed list
   - Show active filter state

### Technical Requirements

- Use React functional components
- Implement state management with useState hook
- Use props to pass data between components
- Implement conditional rendering
- Use JSX for all UI elements
- Apply styling using at least two different methods (inline styles, CSS classes, or CSS Modules)
- Ensure responsive design for mobile and desktop

### Project Structure

```
todo-app/
├── src/
│   ├── components/
│   │   ├── TodoForm.jsx
│   │   ├── TodoList.jsx
│   │   ├── TodoItem.jsx
│   │   └── TodoFilter.jsx
│   ├── App.jsx
│   ├── index.jsx
│   └── App.css (or CSS Modules)
├── package.json
└── README.md
```

## Acceptance Criteria

- [ ] Application runs without errors
- [ ] All core features are implemented and functional
- [ ] Code is well-structured and follows React best practices
- [ ] Components are properly separated and reusable
- [ ] State is managed correctly with useState
- [ ] Props are used appropriately for data flow
- [ ] JSX is used correctly for rendering
- [ ] Styling is applied and responsive
- [ ] No console errors or warnings

## Scoring System

### Functionality (40 points)
- Add Todo: 8 points
- Display Todo List: 8 points
- Toggle Completion: 8 points
- Delete Todo: 8 points
- Filter Todos: 8 points

### Code Quality (30 points)
- Component Structure: 10 points
- State Management: 10 points
- Props Usage: 10 points

### React Concepts Implementation (20 points)
- JSX Usage: 5 points
- Conditional Rendering: 5 points
- List Rendering with Keys: 5 points
- Hooks Implementation: 5 points

### Styling & UI (10 points)
- Visual Design: 5 points
- Responsive Design: 5 points

### Total: 100 points

### Grading Scale
- 90-100: Excellent - All requirements met with high quality code
- 80-89: Good - Most requirements met with good code quality
- 70-79: Satisfactory - Core functionality works but needs improvements
- Below 70: Needs significant rework

## Submission Instructions

1. Create a new GitHub repository for this project
2. Implement the Todo application following the requirements
3. Include a README.md with:
   - Project description
   - How to run the application
   - Features implemented
   - Any additional notes
4. Submit the GitHub repository link
5. Ensure the application can be run with `npm install` and `npm start`

## Time Limit

Complete this project within 6 hours of active development time. Focus on core functionality first, then add styling and polish.

## Notes

- Use Create React App or Vite for project setup
- You may use any additional libraries if needed, but core functionality should be built with vanilla React
- Pay attention to React best practices and performance
- Test your application thoroughly before submission