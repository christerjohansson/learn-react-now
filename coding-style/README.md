# Coding Styles

When doing code reviews, developers rarely get enough time to truly understand each line of code we’re reviewing. Instead, we have to quickly ponder the different situations where that code might fail.

So every time I review code, I look for certain points to help me quickly understand the code.
This chapter will help you understand how you can write better code, so that other developers can better understand it. 

This chapter will give you a quick introduction to certain techniques I use while designing my components, and show you how you can do the same. Note that we will stick mostly to ReactJS here, but that some of these points which may apply to using other JavaScript libraries as well.

## Tip 1: Always use prop-types to define all the props in your components

[prop-types](https://github.com/facebook/prop-types) is runtime type checking for React props and similar objects. prop-types will help you check if the desired type of prop is getting passed into your component or not.

If the proper type of a specific prop is not passed into your component, then the package will throw a warning in the console of your browser. In the above pen, you can check the console and it will throw the following warning:

>"Warning: Failed prop type: Invalid prop `message` of type `string` supplied to `Hello`, expected `array`. in Hello"

From the above warning message, it is pretty clear that we are passing a string to the Hello component but the component expects the prop message to be of type array.

## Tip 2: Use displayName to define a particular name to your component

The [displayName](https://reactjs.org/docs/react-component.html#displayname) string is used in debugging messages. If you don’t use displayName in your components, you should use it from now on.

Normally, if you debug your component using the React developer tools, you will see the components because it’s inferred from the name of the function or class that defines the component.

However, if you have a situation, where you have two components with the same name (button, dropdown, etc.), then you might need to rename your components. Otherwise, you won’t be able to distinguish between them.

You can solve the above problem using displayName. You simply rename one of the components using displayName.

In the above example, you can see that even though the name of the class is Component, you will see the name “Hello” in the React developer tool because it has Hello as its displayName.

This is very useful for debugging purposes and is often over-looked.

## Tip 3: Use a linter to make your code easier to review
If you care about your sanity, then you should use a linter on your codebase.
Linters will help you make your code similar to other fellow developers in your company. By follow a strict set of rules, you can be certain that the whole code base will be consistent.

For instance, you can force other developers to use semicolons at the end of a line. If they don’t, then the linter will throw an error or a warning based on your settings.

The linter which I follow mostly is [ESLint](https://eslint.org/) but you can choose anyone that suits your needs.

## Tip 4: Review your own code before creating a pull request
Whether you are fixing a bug or developing a new feature, chances are that you’ll push your changes and create a pull request quickly when you’re in a hurry. The problem with that is you don’t even get to review your own changes. As a result, you might miss some places which you can refactor and make it better.

From my experience, after reviewing my own changes, sometimes, I could make it more performant, split bigger functions into multiple smaller ones and make the code more modularised. Earlier, I never used to review my own code. But practicing this habit, I feel that it improves my coding and it might help you too.

## Tip 5: Your first draft is not always the best one
Many of you already know this. The first iteration is not always the best one.
You should look at your first iteration of coding and think about the features that you might have missed.

One way to fix this could be doing a Test Driven Development (TDD), which is a great practice but is seldom followed. If you follow a TDD approach, you first iteration can be the best one. But you should look for a better approach.

Take your time to think about how you want to proceed even before writing a single line of code and when you’re done with implementing a feature or fixing a bug, look at your changes and think how you can make it better.

## Tip 6: Split your code into multiple smaller functions
Splitting your bigger functions into multiple smaller functions will make the smaller functions more reusable. They will also become much easier to test.

You can also create many utility files which can help you remove duplicate code from multiple files. After creating multiple files, look at them and you will see that there are many duplicated lines of code. You can take these lines are create a utility file. You can then reuse the same utility file across multiple files.

## Tip 7: Create multiple files instead of writing a big file
Reviewing one big file is always harder than reviewing multiple smaller files.

If you split your code into multiple smaller files and each file contains only one logic, then it becomes very easy for the reviewer to review that file.

## Tip 8: Be very careful while naming your files
Another thing you should remember here is that if you name your files according to the job that they perform, it will also help you in the future as well as other developers to understand what the file actually does.

After looking at the name of the file, other developers should understand what the file is supposed to do. For instance, dropdown.js is a good name but it’s very generic and if you use it in multiple places in the same directory, you might name it like topDropdown.js, bottomDropdown.js, which is bad.

A better way will be to prefix them with the job that they are supposed to perform. For instance, userDropdown.js, fileDropdown.js, etc.

## Tip 9: Always write tests for your code
I can’t stress enough the importance of this point. Tests complete your code.

After developing a feature, you might feel that it works and it does work. But there can be (and most probably will) edge cases where it might not work. Tests will help you identify those cases.

It’s obvious that writing test cases will increase the time that you need to write your code. But, it will always help you eliminate potential bugs that might crop up in the future. You should take the time to write tests if you care about your application.

## Tip 10: Don’t over-use the error handling lifecycle hook
React 16 introduced a better way of [handling errors](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html) using a feature called Error Boundaries.

Essentially, error boundaries are React components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI instead of the component tree that crashed.

If the logic for the fallback UI is present in your ErrorBoundary component, then you can encapsulate your component inside that ErrorBoundary component.

```
<ErrorBoundary>  
    <YourComponent />
</ErrorBoundary>
```

This is a nice way in which you can show a fallback UI for your errors. But you don’t need to wrap all your components with an ErrorBoundary component. You can put your ErrorBoundary component only in a [few strategic places](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html#where-to-place-error-boundaries) where you need them.

**Disclaimer:**

This chapter is purely focused on familiarizing yourself with different coding styles. It is not a set of coding standards nor conventions.
