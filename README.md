# Lab 02 | Stock Portfolio Dashboard

## Table of Contents

1. [Lab 02 | Stock Portfolio Dashboard](#lab-02--stock-portfolio-dashboard)
   - [Overview](#overview)
   - [Instructions](#instructions)
2. [Guidance and Testing](#guidance-and-testing)
   - [Bonus Points](#bonus-points)
   - [Submission](#submission)
3. [Getting Started with GitHub and Codespaces](#getting-started-with-github-and-codespaces)
   - [Step 1: Fork the Repository](#step-1-fork-the-repository)
   - [Step 2: Open the Repository in Codespaces](#step-2-open-the-repository-in-codespaces)
   - [Step 3: Complete the Lab Assignment](#step-3-complete-the-lab-assignment)
   - [Step 4: Submit Your Work via Pull Request](#step-4-submit-your-work-via-pull-request)

## Overview

In this labe, you will use JavaScript, DOM manipulations and event handling to create a dashboard
that is used for examining user stock portfolio holdings. The dashboard mark up and CSS styles will be provided for you.
You will be responsible for writing the JavaScript code to make the dashboard functional.

The dashboard functions as follows:

- The user can select a user from the user list. When a user is selected, the user’s portfolio is displayed.
- The user can select a stock from the stock list. When a stock is selected, the stock’s information is displayed.
- The user can modify a user's information updating the user's information in the form, and clicking save.
- The user can delete a user from the user list. When a user is deleted, the user’s portfolio is removed from the dashboard.

The goal of this lab is to familarize you with the DOM and event handling. This should provide you with some context as to
some of the problems which ReactJS is designed to solve.

Below is a screenshot of the completed project:

## Instructions

1. Open the Replit classroom for this lab. You will see the core files, `index.html` and `app.js`. You should not
need to update the `index.html` file. All of your code will go in the `app.js` file. There are also four JSON data files: `users.json`, `stocks-complete.json`, `single-user.json`, and `single-stock.json`. These can be found in the `data` directory.
2. You have been provided with 2 JSON data files and two javascript files that contain JSON: `users.js`, `stocks-complete.js`, `stocks-formatted.json` and `single_user.json`. The file `users.js` contains an array of objects consisting of an individual user’s information and the stocks he or she owns (i.e., his or her portfolio). Information about each stock/company is contained in `stocks-complete.js`. Examine `single-user.json`, which contains a single example of the objects contained in `users.js` (and will not be used by your application since it is only provided for illustration purposes).
3. Let's begin by modifying `app.js` and add a DOMContentLoaded event handler. This will ensure that our code is excuted once all of the assets (json, css, html) have been loaded. All of your code will be inside that handler. Your handler will need to use the `JSON.parse()` method to transform the JSON data in the two JSON data files into JavaScript objects.

```js
document.addEventListener('DOMContentLoaded', () => {
  // function will go here
});
```

4. Now let's access some of the data. The `data/users.js` and `data/stocks-complete.js` files are included in our `index.hml` using the `script` tag. This means that the variables assigned within the files (`stockContent` and `userContent`) are available to us in our `app.js` file. Using `JSON.parse()` we can convert the JSON data into a JavaScript object.

```js
//  note add this inside of our DOMContentLoaded event handler
const stocksData = JSON.parse(stockContent);
const userData = JSON.parse(userContent);
```

5. With the users and stock data accessible. Let's start building out the users list on the side of the app. We will start by creating a function that will generate the list of users. We will loop through the `userData` array and create a list item for each user. We will also add an `id` attribute to each list item so that we can easily access the user's information later. This function will be defined _outside_ of the DOMContentLoaded event handler.

```js
/**
 * Loops through the users and renders a ul with li elements for each user
 * @param {*} users 
 */
function generateUserList(users) {
  // get the list element and for each user create a list item and append it to the list
  const userList = document.querySelector('.user-list');
  
  users.map(({user, id}) => {
    const listItem = document.createElement('li');
    listItem.innerText = user.lastname + ', ' + user.firstname;
    listItem.setAttribute('id', id);
    userList.appendChild(listItem);
  });
}
```

6. The list renders, but it does nothing when we click on each user. For this step, let's configure the form to populate with the user's data when we click on a user from the user list.
We will need to create an event listener that handles the `click` event, and register it on the list. Using event delegation, we can register the event listener on the list, and then use the `event.target` to determine which user was clicked. We will then use the `id` attribute we added to the list item to find the user's information in the `userData` array. Once we have the user's information, we can populate the form with the user's data.

Add the below function to our `app.js` file. This function will be defined _outside_ of the DOMContentLoaded event handler.

```js
/**
 * Handles the click event on the user list
 * @param {*} event 
 */
function handleUserListClick(event, users) {
  // get the user id from the list item
  const userId = event.target.id;
  // find the user in the userData array
  // we use a "truthy" comparison here because the user id is a number and the event target id is a string
  const user = users.find(user => user.id == userId);
  // populate the form with the user's data
  populateForm(user);
}
```

Now, let's create the populateForm function. This function will take in a user object and populate the form with the user's data. This function will be defined _outside_ of the DOMContentLoaded event handler.

```js
/**
 * Populates the form with the user's data
 * @param {*} user 
 */
function populateForm(data) {
  // use destructuring to get the user and id from the data object
  const { user, id } = data;
  document.querySelector('#userID').value = id;
  document.querySelector('#firstname').value = user.firstname;
  document.querySelector('#lastname').value = user.lastname;
  document.querySelector('#address').value = user.address;
  document.querySelector('#city').value = user.city;
  document.querySelector('#email').value = user.email;
}
```

Lastly, let's register the event listener in our `generateUserList` function. We will add the event listener to the list element and register the `handleUserListClick` function as the callback.

```js
/**
 * Loops through the users and renders a ul with li elements for each user
 * @param {*} users 
 */
function generateUserList(users) {
  // get the list element and for each user create a list item and append it to the list
  const userList = document.querySelector('.user-list');
  users.map(({ user, id }) => {
    const listItem = document.createElement('li');
    listItem.innerText = user.lastname + ', ' + user.firstname;
    listItem.setAttribute('id', id);
    userList.appendChild(listItem);
  });

  // register the event listener on the list
  userList.addEventListener('click', (event) => handleUserListClick(event, users));
}
```

Great - so now we should have a form that populates with all of the data when we click on a user from the list. Let's move on to the next step.

7. For this section, we will now render the portfolio items for the user. This will have us modifying the `handleUserListClick` to call another function which will render the portfolio items. First let's make the stock data accessible to the `handleUserListClick` function. We will add a parameter to the function and pass in the stock data.

```js
// update the call to generateUserList to pass in the stock data

  generateUserList(userData, stocksData);

// update the method signature of generateUserList to accept the stock data
function generateUserList(users, stocks) {
  // ....
}
// update the method signature of handleUserListClick to accept the stock data
function handleUserListClick(event, users, stocks) {
  // ....
}
// Finally, update the call to handleUserListClick to pass in the stock data
  userList.addEventListener('click', (event) => handleUserListClick(event, userData, stocksData));
```

Now, let's create the `renderPortfolio` function. This function will take in a user object and render the portfolio items for the user. This function will be defined _outside_ of the DOMContentLoaded event handler.

```js
/**
 * Renders the portfolio items for the user
 * @param {*} user 
 */
function renderPortfolio(user, stocks) {
  // get the user's stock data
  const { portfolio } = user;
  // get the portfolio list element
  const portfolioDetails = document.querySelector('.portfolio-list');
  // clear the list from previous render
  portfolioDetails.innerHTML = '';
  // map over portfolio items and render them
  portfolio.map(({ symbol, owned }) => {
    // create a list item and append it to the list
    const symbolEl = document.createElement('p');
    const sharesEl = document.createElement('p');
    const actionEl = document.createElement('button');
    symbolEl.innerText = symbol;
    sharesEl.innerText = owned;
    actionEl.innerText = 'View';
    actionEl.setAttribute('id', symbol);
    portfolioDetails.appendChild(symbolEl);
    portfolioDetails.appendChild(sharesEl);
    portfolioDetails.appendChild(actionEl);
  });
}
```

We then need to call the `renderPortfolio` function from the `handleUserListClick` function.

```js
/**
 * Handles the click event on the user list
 * @param {*} event 
 */
function handleUserListClick(event, users, stocks) {
  // ...
  // render the portfolio items for the user
  renderPortfolio(user, stocks);
}
```

Great - so now we should have a form that populates with all of the data when we click on a user from the list, and we should have a list of portfolio items for the user. Let's move on to the next step.
8. For this section, we will add the functionality to view the stock information when we click on the view button. We will start by creating the `viewStock` function. This function will take in a symbol and render the stock information for that symbol. We'll need to register an event listener on the view button and call the `viewStock` function when the button is clicked.

```js
/**
 * Renders the stock information for the symbol
 * @param {*} symbol 
 */
function viewStock(symbol, stocks) {
  // begin by hiding the stock area until a stock is viewed
  const stockArea = document.querySelector('.stock-form');
  if (stockArea) {
      // find the stock object for this symbol
      const stock = stocks.find( function (s) { return s.symbol == symbol;});

      document.querySelector('#stockName').textContent = stock.name;
      document.querySelector('#stockSector').textContent = stock.sector;
      document.querySelector('#stockIndustry').textContent = stock.subIndustry;
      document.querySelector('#stockAddress').textContent = stock.address;

      document.querySelector('#logo').src = `logos/${symbol}.svg`;
  }
}
```

And then register the `viewStock` as an event listener for the view button. Instead of registering it on each button, we will register it on the parent element and use event delegation to handle the click event.

```js
/**
 * Handles the click event on the user list
 * @param {*} event 
 */
function renderPortfolio(event, users, stocks) {
  // ...
  // register the event listener on the list
  portfolioDetails.addEventListener('click', (event) => {
    // let's make sure we only handle clicks on the buttons
    if (event.target.tagName === 'BUTTON') {
      viewStock(event.target.id, stocks);
    }
  });
}
```

9.

We almost have a fully functioning dashboard. Now for the last piece, we want to have the form update the user details when we modify and save the user. We also want to be able to delete a user from the list. Let's start with the delete function. We will add an event listener to the delete button that will slice the user from the user's array. Because we will need to call the `generateUserList` function after deleting the user, we will keep the delete callback in the main event handler scope, where the `stockData` and `userData` are accessible.
Add this to the bottom of the main event handler.

```js
...
  // Register the event listener on the delete button
  deleteButton.addEventListener('click', (event) => {
    // we don't want the form to submit (since we will lose form state)
    event.preventDefault();

    // find the index of the user in the data array 
    const userId = document.querySelector('#userID').value;
    const userIndex = userData.findIndex(user => user.id == userId);
    // remove the user from the array
    userData.splice(userIndex, 1);
    // render the user list
    generateUserList(userData, stocksData);
  });
```

If you run this now, you'll see that the `.user-list` data will stack, this is because the `generateUserList` function is appending the list items to the list. We need to clear the list before we render the new list. Let's update the `generateUserList` function to clear the list before rendering the new list.

```js
/**
 * Loops through the users and renders a ul with li elements for each user
 * @param {*} users 
 */
function generateUserList(users, stocks) {
  // get the list element and for each user create a list item and append it to the list
  const userList = document.querySelector('.user-list');
  // clear out the list from previous render
  userList.innerHTML = '';
  // .. rest of function
}
```

Now, let's add the save functionality. We will add an event listener to the save button that will update the user details. We will keep the save callback in the main event handler scope, where the `stockData` and `userData` are accessible as well.

```js
// Register the event listener on the save button
  saveButton.addEventListener('click', (event) => {
    // we don't want the form to submit (since we will lose form state)
    e.preventDefault();

    // find the user object in our data
    const id = document.querySelector('#userID').value;

    for (let i=0; i<users.length; i++) {
        // found relevant user, so update object at this index and redisplay
        if (users[i].id == id) {

            users[i].user.firstname = document.querySelector('#firstname').value;
            users[i].user.lastname = document.querySelector('#lastname').value;
            users[i].user.address = document.querySelector('#address').value;
            users[i].user.city = document.querySelector('#city').value;
            users[i].user.email = document.querySelector('#email').value;     

            generateUserList(users, stocks);
        }
    }
  });
  ```

  Now we should have a fully functioning dashboard. Let's test it out.

## Guidance and Testing

1. The steps above should walk you through creating the layout to match the mockup. You can review the video walkthrough for further guidance.

## Submission

Once you have completed the lab, please submit your code to the Replit classroom. You can do this by clicking the "Share" button in the top right corner of the Replit editor. Then, click the "Share to Classroom" button. You should see a list of classes that you are enrolled in. Select the class that you are enrolled in and click the "Share" button. You should see a message that your code has been shared with the class. You can now close the share window.

# Getting Started with GitHub and Codespaces

Welcome to the course! In this guide, you’ll learn how to set up your coding environment using GitHub and Codespaces. By following these steps, you’ll be able to work on your lab assignments, write and test your code, and submit your work for review. Let's get started!

## Step 1: Fork the Repository

Forking a repository means making a copy of it under your GitHub account. This allows you to make changes without affecting the original project.

1. **Open the Repository**: Start by navigating to the GitHub repository link provided by your instructor.
2. **Click "Fork"**: In the top-right corner, find the “Fork” button and click it.
3. **Select Your Account**: Choose your GitHub account as the destination for the fork. Once done, you’ll be redirected to your forked copy of the repository.

   > **Tip**: Make sure you’re logged into your GitHub account, or you won’t see the option to fork!

## Step 2: Open the Repository in Codespaces

With your forked repository ready, you can now set up a development environment using Codespaces. This setup provides a pre-configured environment for you to code in, with everything you need to complete the lab.

1. **Open the Codespaces Menu**:
   - In your forked repository, click the green "Code" button, then switch to the "Codespaces" tab.
2. **Create a Codespace**:
   - Click on "Create codespace on main" to start the setup.
3. **Wait for Codespaces to Load**:
   - It may take a few minutes for Codespaces to create and configure your environment. Be patient, as it’s setting up all the tools you’ll need.
4. **Start Coding**:
   - Once the setup is complete, Codespaces will automatically open a new browser tab where your code will be ready to run. You’ll be able to see the code and any outputs as you go through the lab assignment.

## Step 3: Complete the Lab Assignment

Inside the Codespaces environment, you’ll find all the files and instructions you need. Follow the steps outlined in the README file to complete your assignment.

1. **Read the Instructions**: Carefully go through the README file to understand the tasks you need to complete.
2. **Edit the Code**: Make the necessary changes to the code files as instructed.
3. **Run and Test Your Code**: Use the terminal and editor within Codespaces to run your code and make sure everything works as expected.

   > **Hint**: If you’re stuck, try reviewing the README file again or refer to any resources provided by your instructor.

## Step 4: Submit Your Work via Pull Request

Once you’ve completed the assignment, it’s time to submit your work. You’ll do this by creating a pull request, which is a way to propose your changes to the original repository.

1. **Commit Your Changes**:
   - Save your work by committing your changes. In Codespaces, go to the Source Control panel, write a commit message, and click "Commit" to save your changes.
2. **Push to Your Fork**:
   - After committing, click "Push" to upload your changes to your forked repository on GitHub.
3. **Create a Pull Request**:
   - Go back to your GitHub repository, and you’ll see an option to “Compare & pull request.” Click it to start your pull request.
   - Include your name in the pull request description so your instructor knows who submitted it.
4. **Submit the Pull Request**:
   - Click "Create pull request" to submit your work for review. Your instructor will be notified and can review your work.

And that’s it! You’ve now completed your first lab assignment using GitHub and Codespaces. Well done!

### Additional Steps

1. Open the terminal in Codespaces.
2. Run the following commands to install dependencies and start the development server:

    ```sh
    npm install
    npm run dev
    ```

3. You can now view the project in the browser by clicking the "Application" port in the Ports panel.[887u7]

Follow the instructions in the previous sections to complete the lab.
