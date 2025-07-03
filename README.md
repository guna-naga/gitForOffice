// Select the parent container with class '3_div'
const parentContainer = document.querySelector('.3_div');

// Select all elements with class 'four-heading-line'
const headingElements = document.querySelectorAll('.four-heading-line');

// Append each heading element to the bottom of the 3_div container
if (parentContainer && headingElements.length > 0) {
  headingElements.forEach(element => {
    parentContainer.appendChild(element);
  });
}
