let currentStepIndex = 0;

const amountFields = [
    { ccy: 'COLL_CCY', amt: 'COLL_AMT'},
    { ccy: 'USANCE_CCY', amt: 'DRFT_AMT2'},
    { ccy: 'SIGHT_CCY', amt: 'DRFT_AMT'}
];

$(document).ready(function () {

    // Function to handle checkboxes
    function renderCheckBox($input, $label) {
        var checkboxWrapper = $('<div class="checkbox-wrapper"></div>');
        
        if ($label && $label.length) {
            // Standard layout: checkbox + label
            checkboxWrapper.append($input);
            checkboxWrapper.append($label);
        } else {
            checkboxWrapper.append($input);
        }

        return checkboxWrapper;
    }

    $('div[id$="_div"]').each(function () {
        var targetDiv = $(this);
        var rows = targetDiv.find('tr');
        var count = 0;

        targetDiv.find('#messageDisplay').remove();
        var newRow = $('<div class="row-container"></div>');

        rows.each(function () {
            var $row = $(this);
            var inputs = $row.find('input,radio, select, textarea, input[type="hidden"], checkbox');
            var labels = $row.find('.FldLabel, .FldLabel.star span');
            var buttons = $row.find('input[type="button"], button'); 

            inputs.each(function (index) {
                var $input = $(this);
                var $label = $(labels[index]);

                if ($input.is('input[type="hidden"]')) {
                    targetDiv.append($input);  
                    return;  
                }

                if ($label && $label.is('span')) {
                    var spanWrapper = $('<div class="span-wrapper"></div>');
                    spanWrapper.append($label);  
                    newRow.append(spanWrapper);  
                    return;  
                }

                // Handle radio buttons
                else if($input.attr('type') === 'radio'){
                    var fieldWrapper = $('<div class="radio-group"></div>');
                    fieldWrapper.append($label);
                    var radioGroup = $row.find(`input[name="${$input.attr('name')}"]`);
                    var radioWrapper = $('<div class="radio-wrapper"></div>');
                    radioGroup.each(function () {
                        var $radio = $(this);
                        var radioLabel = $row.find(`label[for="${$radio.attr('id')}"]`);                            
                        radioWrapper.append($radio); 
                        radioWrapper.append(radioLabel);                            
                    });
                    fieldWrapper.append(radioWrapper); 
                    newRow.append(fieldWrapper);
                    count++;
                } 

                // ✅ Handle checkboxes
                else if ($input.attr('type') === 'checkbox') {
                    var fieldWrapper = $('<div class="field-wrapper"></div>');
                    var checkboxField = renderCheckBox($input, $label);
                    fieldWrapper.append(checkboxField);

                    var messageDisplay = $('<div>', {
                        id: 'messageDisplay_' + $input.attr('name'),
                        class: 'error-message',
                        text: ''
                    });
                    fieldWrapper.append(messageDisplay);

                    newRow.append(fieldWrapper);
                    count++;
                }

                else if ($input.length && $label.length) {
                    var fieldWrapper = $('<div class="field-wrapper"></div>');
                    fieldWrapper.append($label);

                    if ($input.hasClass('INT_P') || $input.hasClass('AMT_P') || $input.hasClass('CHAR_P') || $input.hasClass('TIME_P')) {
                        $input.attr('disabled', true); 
                    }

                    if ($input.hasClass('DATE_M') || $input.hasClass('DATE_P') || $input.hasClass('DATE_O')) {
                        $('#ICO_' + $input.attr('id')).remove();
                        $input.datepicker({
                            showOn: "focus",
                            dateFormat: 'dd/mm/yy'
                        });

                        var calendarIcon = $('<svg>', {
                            width: "22",
                            height: "20",
                            viewBox: "0 0 22 20",
                            fill: "none",
                            class: "calendarbutton",
                            id: "ICO_" + $input.attr('id'),
                            title: "Pick a Date",
                            html: `<path d="M1.6665 7H20.3332M1.6665 7V15.8002C1.6665 16.9203 1.6665 17.4801 1.92082 17.9079C2.14453 18.2842 2.50122 18.5905 2.94027 18.7822C3.43891 19 4.092 19 5.39623 19H16.6034C17.9077 19 18.5598 19 19.0585 18.7822C19.4975 18.5905 19.8554 18.2842 20.0791 17.9079C20.3332 17.4805 20.3332 16.9215 20.3332 15.8036V7M1.6665 7V6.2002C1.6665 5.08009 1.6665 4.51962 1.92082 4.0918C2.14453 3.71547 2.50122 3.40973 2.94027 3.21799C3.43939 3 4.09328 3 5.40007 3H6.33317M20.3332 7V6.19691C20.3332 5.07899 20.3332 4.5192 20.0791 4.0918C19.8554 3.71547 19.4975 3.40973 19.0585 3.21799C18.5593 3 17.9069 3 16.6001 3H15.6665M15.6665 1V3M15.6665 3H6.33317M6.33317 1V3" stroke="#747474" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>`
                        });

                        var dateWrapper = $('<div class="date-wrapper"></div>');
                        dateWrapper.append($input);
                        dateWrapper.append(calendarIcon);

                        calendarIcon.on('click', function () {
                            $input.focus();
                        });

                        fieldWrapper.append(dateWrapper);
                    } 
                    else if ($input.is('textarea')) {
                        $input.removeAttr('style');
                        $input.addClass('textarea-field');
    
                        var textareaWrapper = $('<div class="textarea-wrapper"></div>');
                        textareaWrapper.append($input);
    
                        var cols = $input.attr('cols') || 50;
                        var rows = $input.attr('rows') || 5;
                        var maxLength = cols * rows;
                        $input.attr('maxlength', maxLength); 
    
                        var charCountDisplay = $('<div class="char-count">0/' + maxLength + ' characters</div>');
    
                        $input.on('input', function () {
                            var currentLength = $(this).val().length;
                            charCountDisplay.text(currentLength + '/' + maxLength + ' characters');
                        });
    
                        var textareaFieldWrapper = $('<div class="textarea-field-wrapper full-row"></div>');
                        textareaFieldWrapper.append($label);
                        textareaFieldWrapper.append(textareaWrapper);
                        textareaFieldWrapper.append(charCountDisplay);
    
                        var textareaRow = $('<div class="row-container textarea-row"></div>');
                        textareaRow.append(textareaFieldWrapper);
                        targetDiv.append(textareaRow); 
    
                        newRow = $('<div class="row-container"></div>');
                        count = 0; 
                    } 
                    else if ($input.is('select')) {
                        var selectWrapper = $('<div class="select-wrapper"></div>');
                        $input.addClass('select-field');
                        selectWrapper.append($input);
                        fieldWrapper.append(selectWrapper);
                    } 
                    else {
                        $input.addClass('input-field');
                        fieldWrapper.append($input);
                    }

                    buttons.each(function () {
                        var $button = $(this);
                        if ($button.is('input[type="button"]')) {
                            var buttonClass = $button.attr('class');
                            var buttonText = $button.val();
                            var buttonTitle = $button.attr('title'); 
                            var buttonOnClick = $button.attr('onclick'); 
                            var newButton;

                            if (buttonClass === 'search' || buttonText === 'Search BIC/IFSC') {
                                buttonClass = 'search-button';
                                newButton = $('<button>', {
                                    id: $button.attr('id'),
                                    class: buttonClass,
                                    title: buttonTitle,
                                    text: 'Search',
                                    click: function (event) {
                                        event.preventDefault();
                                        if ($button.attr('onclick')) {
                                            eval($button.attr('onclick'));
                                        }
                                    }
                                });
                            } else if (buttonText === 'Reset' || buttonText === 'Reset BIC/IFSC') {
                                buttonClass = 'clear-button'; 
                                var clearSVG = `
                                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none">
                                        <path d="M18.3002 5.70997C17.9102 5.31997 17.2802 5.31997 16.8902 5.70997L12.0002 10.59L7.11022 5.69997C6.72022 5.30997 6.09021 5.30997 5.70021 5.69997C5.31021 6.08997 5.31021 6.71997 5.70021 7.10997L10.5902 12L5.70021 16.89C5.31021 17.28 5.31021 17.91 5.70021 18.3C6.09021 18.69 6.72022 18.69 7.11022 18.3L12.0002 13.41L16.8902 18.3C17.2802 18.69 17.9102 18.69 18.3002 18.3C18.6902 17.91 18.6902 17.28 18.3002 16.89L13.4102 12L18.3002 7.10997C18.6802 6.72997 18.6802 6.08997 18.3002 5.70997Z" fill="#747474"/>
                                    </svg>`;
                                newButton = $('<button>', {
                                    id: $button.attr('id'),
                                    class: buttonClass,
                                    title: buttonTitle,
                                    html: clearSVG,
                                    click: function (event) {
                                        event.preventDefault();
                                        if ($button.attr('onclick')) {
                                            eval($button.attr('onclick'));
                                        }
                                    }
                                });
                            } else {
                                newButton = $('<button>', {
                                    id: $button.attr('id'),
                                    class: buttonClass,
                                    title: buttonTitle,
                                    text: buttonText,
                                    click: function (event) {
                                        event.preventDefault();
                                        if ($button.attr('onclick')) {
                                            eval($button.attr('onclick'));
                                        }
                                    }
                                });
                            }

                            if (buttonOnClick) {
                                newButton.attr('onclick', buttonOnClick);
                            }
                            fieldWrapper.append(newButton);
                        } else {
                            fieldWrapper.append($button);
                        }
                    });

                    var messageDisplay = $('<div>', {
                        id: 'messageDisplay_' + $input.attr('name'),
                        class: 'error-message',
                        text: ''
                    });
                    fieldWrapper.append(messageDisplay);

                    if (!$input.is('textarea')) {
                        newRow.append(fieldWrapper);
                        count++;
                        if (count === 3) {
                            targetDiv.append(newRow);
                            newRow = $('<div class="row-container"></div>');
                            count = 0;
                        }
                    }
                }
            });
        });

        if (newRow.children().length > 0) {
            targetDiv.append(newRow);
        }

        function removeEmptyRowsAndCells() {
            targetDiv.find('td').each(function () {
                var $td = $(this);
                const fldId = $td.find('input').first().attr('id'); 
                var isEmpty = $td.text().trim() === "" || $td.html().replace(/\s|&nbsp;/g, '') === "";
                var amtFld = amountFields.find(section => section.amt === fldId);
                if (isEmpty && !amtFld) {
                    $td.remove();
                }
            });
            targetDiv.find('tr').each(function () {
                var $tr = $(this);
                if ($tr.find('td').length === 0) {
                    $tr.remove();
                }
            });
        }

        function reArrangeAmtFields() {
            $.each(amountFields, function(_, sec) {
                var ccy = $('#' + sec.ccy);
                var amt = $('#' + sec.amt);
                var ccyDiv = ccy.parent();
                ccyDiv.css({
                    display: 'flex',alignItems: 'center',gap: '2%',width:'98%'
                });
                ccy.css({
                    flex: '0 0 24%',maxWidth:'24%',minWidth:'24%'
                });
                amt.css({
                    flex: '0 0 74%',maxWidth:'74%',minWidth:'74%'
                });
                if (!amt.parent().is(ccyDiv)) {
                    ccyDiv.append(amt);
                }
            });
        }

        removeEmptyRowsAndCells();
        reArrangeAmtFields();

        function wrapErrorRows() {
            $('tr[id^="error_O"]').each(function () {
                var $errorRow = $(this);
                var errorFieldId = $errorRow.find('input').attr('id'); 
                var correspondingInput = $('#' + errorFieldId.replace('error_O_', '')); 

                if (correspondingInput.length) {
                    var $inputFieldWrapper = correspondingInput.closest('.field-wrapper');
                    var errorWrapper = $('<div class="error-wrapper"></div>');
                    var errorContent = $errorRow.find('.errorClass');

                    if (errorContent.length) {
                        var errorDiv = $('<div class="errorClass"></div>').text(errorContent.val());
                        errorWrapper.append(errorDiv); 
                        $inputFieldWrapper.append(errorWrapper); 
                        $errorRow.remove(); 
                    }
                }
            });
        }

        wrapErrorRows();
    });
});