// Select the target container by ID
const targetContainer = document.getElementById('TOTAL_AMT_LCY');

// Select all elements with class 'four-heading-line'
const headingElements = document.querySelectorAll('.four-heading-line');

// Append each 'four-heading-line' element to the target container
if (targetContainer && headingElements.length > 0) {
  headingElements.forEach(element => {
    targetContainer.appendChild(element);
  });
}
