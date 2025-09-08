// Move Declaration_Class element to the bottom of 1_div
function moveDeclarationToBottom() {
    var declaration = $('#1_div').find('.Declaration_Class');
    if (declaration.length) {
        $('#1_div').append(declaration); // move it to the bottom
    }
}

// Call this after your rearrangements
moveDeclarationToBottom();