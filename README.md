// Select the element with class 'four-heading-line'
const headingElement = document.querySelector('.four-heading-line');

// Select the last '.row-container' element
const allRowContainers = document.querySelectorAll('.row-container');
const lastRowContainer = allRowContainers[allRowContainers.length - 3];

// Insert the headingElement after the last '.row-container'
if (headingElement && lastRowContainer) {
  lastRowContainer.parentNode.insertBefore(headingElement, lastRowContainer.nextSibling);
}

});
