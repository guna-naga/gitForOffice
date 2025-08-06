$('input[class*="AMT_"]').each(function () {
    var currentAttr = $(this).attr('onchange') || '';

    // If NumberConv(event) is not already part of the attribute, add it
    if (!currentAttr.includes('NumberConv')) {
        var updatedAttr = currentAttr ? currentAttr + '; NumberConv(event);' : 'NumberConv(event);';
        $(this).attr('onchange', updatedAttr);
    }
});
