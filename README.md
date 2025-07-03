function moveFourHeadingLineToBottom() {
    // Select the target container
    const targetContainer = $('#3_div');

    if (targetContainer.length === 0) {
        console.warn('#3_div not found.');
        return;
    }

    // Find all elements with class 'four-heading-line'
    const elementsToMove = $('.four-heading-line');

    // Move each one to the bottom of #3_div
    elementsToMove.each(function () {
        $(this).appendTo(targetContainer);
    });
}
