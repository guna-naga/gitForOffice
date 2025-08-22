let currentStepIndex = 0;

			const amountFields = [
				{ ccy: 'COLL_CCY', amt: 'COLL_AMT'},
				{ ccy: 'USANCE_CCY', amt: 'DRFT_AMT2'},
				{ ccy: 'SIGHT_CCY', amt: 'DRFT_AMT'}
				
			
			];

$(document).ready(function () {
    $('div[id$="_div"]').each(function () {
			var targetDiv = $(this); // Assuming 'this' is the current target div
			var rows = targetDiv.find('tr');
			var count = 0;
		
			// Remove any existing messageDisplay div on the page
			targetDiv.find('#messageDisplay').remove();
		
			// Create a new row container for non-textarea inputs
			var newRow = $('<div class="row-container"></div>');
		
			// Iterating over each row of inputs, selects, and labels
			rows.each(function () {
				var $row = $(this);
				var inputs = $row.find('input,radio, select, textarea, input[type="hidden"], checkbox');
				var labels = $row.find('.FldLabel, .FldLabel.star span');
				//var buttons = $row.find('button'); // Find buttons inside the row
                var buttons = $row.find('input[type="button"], button'); // Find input type=button or button inside the row

				// Iterate over each input/select/textarea
				inputs.each(function (index) {
					var $input = $(this);
					var $label = $(labels[index]);

                    // Handle hidden inputs separately (to avoid visual rendering)
                    if ($input.is('input[type="hidden"]')) {
                        targetDiv.append($input);  // Keep hidden input in form for submission
                        return;  // Skip to the next input
                    }

                    // Handle spans (for additional labels or text inside td)
                    if ($label && $label.is('span')) {
                        var spanWrapper = $('<div class="span-wrapper"></div>');
                        spanWrapper.append($label);  // Display the span element properly
                        newRow.append(spanWrapper);  // Append span in the current row container
                        return;  // Skip further processing for this iteration
                    }

                    
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
                    } else if ($input.length && $label.length) {
						var fieldWrapper = $('<div class="field-wrapper"></div>');
						fieldWrapper.append($label);
		
						// Handling specific classes for inputs and disabling some
						if ($input.hasClass('INT_P') || $input.hasClass('AMT_P') || $input.hasClass('CHAR_P') || $input.hasClass('TIME_P')) {
							$input.attr('disabled', true); // Add class and disable
						}
		
						// Handling date inputs with a custom calendar icon
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
                                //xmlns: "http://www.w3.org/2000/svg",
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
						} else if ($input.is('textarea')) {
                            console.log($input.name);
                            // Handle textarea to occupy a full row
                            $input.removeAttr('style');
                            $input.addClass('textarea-field');
            
                            var textareaWrapper = $('<div class="textarea-wrapper"></div>');
                            textareaWrapper.append($input);
            
                            // Calculate the maxlength based on the cols and rows attributes
                            var cols = $input.attr('cols') || 50; // Default to 50 if not set
                            var rows = $input.attr('rows') || 5;  // Default to 5 if not set
                            var maxLength = cols * rows; // Calculate the maxlength
                            $input.attr('maxlength', maxLength); // Set the maxlength attribute
            
                            // Add a character count display for the textarea
                            var charCountDisplay = $('<div class="char-count">0/' + maxLength + ' characters</div>'); // Show calculated maxLength
            
                            // Update character count on input event
                            $input.on('input', function () {
                                var currentLength = $(this).val().length;
                                charCountDisplay.text(currentLength + '/' + maxLength + ' characters');
                            });
            
                            var textareaFieldWrapper = $('<div class="textarea-field-wrapper full-row"></div>');
                            textareaFieldWrapper.append($label);
                            textareaFieldWrapper.append(textareaWrapper);
                            textareaFieldWrapper.append(charCountDisplay);
            
                            // Append the textarea row directly
                            var textareaRow = $('<div class="row-container textarea-row"></div>');
                            textareaRow.append(textareaFieldWrapper);
                            targetDiv.append(textareaRow); 
            
                            // Reset newRow for the next set of inputs
                            newRow = $('<div class="row-container"></div>');
                            count = 0; // Reset count
                        } else if ($input.is('select')) {
							// For select fields, apply custom styling
							var selectWrapper = $('<div class="select-wrapper"></div>');
							$input.addClass('select-field');
							selectWrapper.append($input);
							fieldWrapper.append(selectWrapper);
						// } else if ($input.is(':radio')) {
						// 	// For radio buttons, apply custom styling
						// 	var radioWrapper = $('<div class="radio-wrapper"></div>');
						// 	//var customRadio = $('<span class="custom-radio"></span>');
							
						// 	$input.addClass('radio-field');
							
						// 	// Append the radio input and custom span into the wrapper
						// 	radioWrapper.append($input);
						// 	//radioWrapper.append(customRadio);
							
						// 	// Find the associated label and move it into the wrapper
						// 	var label = $('label[for="' + $input.attr('id') + '"]');
						// 	radioWrapper.append(label);
							
						// 	// Append the wrapper to the parent container
						// 	$input.closest('td').append(radioWrapper);
						// }
                        } else {
							// Apply input field styling for regular input fields
							$input.addClass('input-field');
							fieldWrapper.append($input);
						}

						// Append buttons next to input fields (replace input[type="button"] with button)
                        buttons.each(function () {
                            var $button = $(this);

                            // If the button is still an input, replace it
                            if ($button.is('input[type="button"]')) {
                                var buttonClass = $button.attr('class');
                                var buttonText = $button.val();
                                var buttonTitle = $button.attr('title'); // Capture title
                                var buttonOnClick = $button.attr('onclick'); // Get onclick event
                                var newButton;

                                // Update class based on conditions
                                if (buttonClass === 'search' || buttonText === 'Search BIC/IFSC') {
                                    // Create Search button
                                    buttonClass = 'search-button'; // Apply the search button class
                                    newButton = $('<button>', {
                                        id: $button.attr('id'),
                                        class: buttonClass, // Apply the updated class
                                        title: buttonTitle, // Set the title
                                        text: 'Search', // Set button text to "Search"
                                        click: function (event) {
                                            event.preventDefault(); // Prevent form submission
                                            // Execute the same onclick function as the input if any
                                            if ($button.attr('onclick')) {
                                                eval($button.attr('onclick'));
                                            }
                                        }
                                    });
                                } else if (buttonText === 'Reset' || buttonText === 'Reset BIC/IFSC') {
                                    // Create Clear button with SVG icon
                                    buttonClass = 'clear-button'; // Apply the clear button class
                                    var clearSVG = `
                                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none">
                                            <path d="M18.3002 5.70997C17.9102 5.31997 17.2802 5.31997 16.8902 5.70997L12.0002 10.59L7.11022 5.69997C6.72022 5.30997 6.09021 5.30997 5.70021 5.69997C5.31021 6.08997 5.31021 6.71997 5.70021 7.10997L10.5902 12L5.70021 16.89C5.31021 17.28 5.31021 17.91 5.70021 18.3C6.09021 18.69 6.72022 18.69 7.11022 18.3L12.0002 13.41L16.8902 18.3C17.2802 18.69 17.9102 18.69 18.3002 18.3C18.6902 17.91 18.6902 17.28 18.3002 16.89L13.4102 12L18.3002 7.10997C18.6802 6.72997 18.6802 6.08997 18.3002 5.70997Z" fill="#747474"/>
                                        </svg>`;

                                    newButton = $('<button>', {
                                        id: $button.attr('id'),
                                        class: buttonClass, // Apply the updated class
                                        title: buttonTitle, // Set the title
                                        html: clearSVG, // Insert SVG as the button content
                                        click: function (event) {
                                            event.preventDefault(); // Prevent form submission
                                            // Execute the same onclick function as the input if any
                                            if ($button.attr('onclick')) {
                                                eval($button.attr('onclick'));
                                            }
                                        }
                                    });
                                } else {
                                    // Create default button
                                    newButton = $('<button>', {
                                        id: $button.attr('id'),
                                        class: buttonClass, // Apply the updated class
                                        title: buttonTitle, // Set the title
                                        text: buttonText, // Use the input's value as button text
                                        click: function (event) {
                                            event.preventDefault(); // Prevent form submission
                                            // Execute the same onclick function as the input if any
                                            if ($button.attr('onclick')) {
                                                eval($button.attr('onclick'));
                                            }
                                        }
                                    });
                                }

                                // If there is an onclick event, bind it to the button
                                if (buttonOnClick) {
                                    newButton.attr('onclick', buttonOnClick);
                                }

                                // Append the newly created button to the fieldWrapper
                                fieldWrapper.append(newButton);
                            } else {
                                // If the element is not an input[type="button"], append it as-is
                                fieldWrapper.append($button);
                            }
                        });



						 // Create a unique messageDisplay div based on the input name or ID
                         var messageDisplay = $('<div>', {
                            id: 'messageDisplay_' + $input.attr('name'),
                            class: 'error-message',
                            text: '' // Initially empty, to be filled with error messages later
                        });
            
                        // Append messageDisplay div inside the field-wrapper
                        fieldWrapper.append(messageDisplay);
		
						// If it's not a textarea, append to newRow
						if (!$input.is('textarea')) {
							newRow.append(fieldWrapper);
							count++;
		
							// Append the new row to the target div every 3 inputs (excluding text areas)
							if (count === 3) {
								targetDiv.append(newRow);
								newRow = $('<div class="row-container"></div>');
								count = 0;
							}
						}
					}
				});
			});
		
			// Append any remaining inputs after the loop (if less than 3 fields are left)
			if (newRow.children().length > 0) {
				targetDiv.append(newRow);
			}
		
		

		// Amount fields ,Add your Currency and amount fields respectively
	
			
        // Function to remove empty <tr> and <td> elements
        function removeEmptyRowsAndCells() {
            // Loop through all <td> elements
            targetDiv.find('td').each(function () {
                var $td = $(this);
				
				const fldId = $td.find('input').first().attr('id'); 
                // Check if the content is empty, including <nobr> and <span> elements that contain only whitespace or &nbsp;
                var isEmpty = $td.text().trim() === "" || $td.html().replace(/\s|&nbsp;/g, '') === "";
                
				var amtFld = amountFields.find(section => section.amt === fldId);
                if (isEmpty && !amtFld) {
                    $td.remove(); // Remove the empty <td>
                }
            });

            // Loop through all <tr> elements
            targetDiv.find('tr').each(function () {
                var $tr = $(this);
                // If the <tr> contains no <td> elements (all empty ones are removed), remove the row
                if ($tr.find('td').length === 0) {
                    $tr.remove();
                }
            });
        }
			
		//Function to format amount fields 
					function reArrangeAmtFields() {
				
			$.each(amountFields, function(_, sec) {
				var ccy = $('#' + sec.ccy);
				var amt = $('#' + sec.amt);
				var ccyDiv = ccy.parent();
				// Apply flex styling
				ccyDiv.css({
					display: 'flex',alignItems: 'center',gap: '2%',width:'98%'
				});
				ccy.css({
					flex: '0 0 24%',maxWidth:'24%',minWidth:'24%'
				});
				amt.css({
					flex: '0 0 74%',maxWidth:'74%',minWidth:'74%'
				});
				// Move amount field inside currency div if not already inside
				if (!amt.parent().is(ccyDiv)) {
					ccyDiv.append(amt);
				}
			});
		}

        // Call the function to remove empty <tr> and <td>
        removeEmptyRowsAndCells();
        // Call the function to rearange amount fields
         reArrangeAmtFields();


        // Function to wrap the error_O rows inside the field-wrapper
        // Function to wrap the error_O rows inside the field-wrapper
        function wrapErrorRows() {
            $('tr[id^="error_O"]').each(function () {
                var $errorRow = $(this);
                var errorFieldId = $errorRow.find('input').attr('id'); // Get the id of the error field
                var correspondingInput = $('#' + errorFieldId.replace('error_O_', '')); // Get the corresponding input field

                if (correspondingInput.length) {
                    // Locate the field-wrapper related to this input
                    var $inputFieldWrapper = correspondingInput.closest('.field-wrapper');

                    // Wrap the error message inside the error-wrapper div
                    var errorWrapper = $('<div class="error-wrapper"></div>');
                    var errorContent = $errorRow.find('.errorClass');

                    if (errorContent.length) {
                        // Create a div to replace the input field for the error message
                        var errorDiv = $('<div class="errorClass"></div>').text(errorContent.val());

                        // Append the errorDiv instead of input
                        errorWrapper.append(errorDiv); // Move error message to div wrapper
                        $inputFieldWrapper.append(errorWrapper); // Append error-wrapper to field-wrapper
                        $errorRow.remove(); // Remove the original error_O row since it's been moved
                    }
                }
            });
        }

// Call the function to wrap error rows
wrapErrorRows();


        // Call the function to wrap error rows
        wrapErrorRows();

        // // Function to check if all mandatory fields within the div are filled 
        // function checkMandatoryFieldsForDiv(divid) {
        //     let allFilled = true;
        //     const div = $(`#${divid}`);

        //     // Check all mandatory fields inside the specific div
        //     div.find('.DATE_M, .CHAR_M, .INT_M, .AMT_M').each(function () {
        //         if ($(this).val() === "" || $(this).val() == null) {
        //             allFilled = false;
        //         }
        //     });
        //     // Show the tick icon and hide the number if all fields are filled
        //     if (allFilled) {
        //         div.find('.tick-icon').show();
        //         div.find('.number-circle').hide();
        //         $('#PRE_BT').show();
        //         $('#NEXT_BT').hide();
        //     } else {
        //         // If not all mandatory fields are filled, show the number
        //         div.find('.tick-icon').hide();
        //         div.find('.number-circle').show();
        //         $('#PRE_BT').hide(); // Hide submit button
        //     }
        // }

        // // Attach the function to change event of mandatory fields within the div
        // targetDiv.find('.DATE_M, .CHAR_M, .INT_M, .AMT_M').on('change keyup', function () {
        //     checkMandatoryFieldsForDiv(targetDiv.attr('id'));
        // });   

        // // Initial check if fields are already filled (e.g., when the page is loaded)
        // checkMandatoryFieldsForDiv(targetDiv.attr('id'));

        // Function to check if all mandatory fields within a specific div are filled


    });

    function checkMandatoryFieldsForDiv(divid) {
        let allFilled = true;
        const div = $(`#${divid}`);
         div.find('.DATE_M, .CHAR_M, .INT_M, .AMT_M').each(function () {
            if ($(this).val() === "" || $(this).val() == null) {
                allFilled = false;  // Mark as false if any mandatory field is empty or null
            }
        }); 
        if (allFilled) {
            div.find('.tick-icon').show();
            div.find('.number-circle').hide();
        } else {
            div.find('.tick-icon').hide();
            div.find('.number-circle').show();
        }

        return allFilled;
    }

    // Function to check if all divs with mandatory fields are filled
    function checkAllMandatoryFields() {
        let allDivsFilled = true;

        // Iterate over all divs that have mandatory fields
        $('div[id$="_div"]').each(function () {
            const divId = $(this).attr('id');

            // Check if the div contains mandatory fields and ensure they're filled
            if ($(this).find('.DATE_M, .CHAR_M, .INT_M, .AMT_M').length > 0) {
                const divFilled = checkMandatoryFieldsForDiv(divId);
                if (!divFilled) {
                    allDivsFilled = false;  // If any div is not fully filled, set this flag to false
                }
            }
        });

        // Show the submit button (PRE_BT) if all mandatory fields in all divs are filled
        if (allDivsFilled) {
            $('#PRE_BT').show();
            $('#NEXT_BT').hide();  // Hide the next button
        } else {
            $('#PRE_BT').hide();
        }
    }

    // Attach the function to change event of mandatory fields within each div
    $('div[id$="_div"]').each(function () {
        const divId = $(this).attr('id');

        // Attach event listeners for change and keyup events on mandatory fields
        $(this).find('.DATE_M, .CHAR_M, .INT_M, .AMT_M').on('change keyup', function () {
            checkMandatoryFieldsForDiv(divId);  // Check if the current div's fields are filled
            checkAllMandatoryFields();  // Re-check all divs when any mandatory field changes
        });
    });

    // Initial check to see if all mandatory fields in all divs are filled when the page loads
    $(document).ready(function() {
        checkAllMandatoryFields();  // Run the initial check when the document is ready
    });


    // Stepper data
	const sections = [
		
        
        { id: 'billscreen', number: 1, title: 'Bill Details', tickId: 'tick1', numberId: 'number1', divid: '1_div' },
        { id: 'Payment', number: 2, title: 'Amendment Details', tickId: 'tick2', numberId: 'number2', divid: '2_div' },
		{ id: 'DocumentsUpload', number: 3, title: 'Documents Upload', tickId: 'tick3', numberId: 'number3', divid: '3_div' }
	
	];

	// Variable to track the active step
	let activeStep = null;

	// Function to initialize the stepper
	function initializeStepper() {
		// Create the wrapper for the stepper and buttons
		const wrapperDiv = $('<div>', { class: 'wrapper-div' });
		const stepperContainer = $('<div>', { class: 'stepper-container' });        

		// Generate each step in the stepper
		sections.forEach((section, index) => {
			const stepDiv = $('<div>', { class: 'step', id: `step_${section.divid}` });
			const stepNumber = $('<div>', { class: 'step-number', text: section.number });
			const stepTitle = $('<span>', { class: 'step-title', text: section.title });
			const sectionDiv = $(`#${section.divid}`);
			const sectionId = $(`#${section.id}`);
            const newSectionId = `step_${section.divid}`;
            //const containerId = $(`#${section.id}_container`);
            const containerId = `#${section.id}_container`;
            console.log($(containerId));

			stepDiv.append(stepNumber, stepTitle);

			// Attach click event for expanding corresponding section and calling `callMe`
			stepDiv.on('click', function () {
				handleStepClick($(containerId), stepDiv, section, newSectionId);
			});

			if (index === 0) {
				stepDiv.addClass('active');
				sectionDiv.show(); // Show the first section
				activeStep = stepDiv; // Set the first step as active
			} else {
				stepDiv.addClass('inactive');
				sectionDiv.hide(); // Hide other sections
			}

			stepperContainer.append(stepDiv);

			// Add a line between steps except the last step
			if (index < sections.length - 1) {
				stepperContainer.append($('<div>', { class: 'step-line' }));
			}
		});

		// Button container initialization (new code)
        const cueCardButton = $('<span>', { class: 'cue-card' });
        const stepByStepVideo = $('<span>', { class: 'step-by-step', text: 'Step by Step Video' });
        const useExistingBG = $('<span>', { class: 'use-existing-bg', text: 'Use Existing BG' ,onclick:'Existing_bg()'});
        const useExistingTemplate = $('<span>', { /* class: 'use-existing-template', text: 'Use Existing Template' ,onclick:'template_IssueLc()' */ });
    
        const cueCardText = $('<span>', { class: 'cue-card-text', text: 'Cue Card' });

        // Add the SVG icon and text to the "Cue Card" button
        const svgCueCardIcon = `
            <svg width="21" height="17" viewBox="0 0 21 17" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M4.74957 13H4.69691C3.57858 13 3.0192 13 2.5918 12.7822C2.21547 12.5905 1.90973 12.2837 1.71799 11.9074C1.5 11.4796 1.5 10.9203 1.5 9.8002V4.2002C1.5 3.08009 1.5 2.51962 1.71799 2.0918C1.90973 1.71547 2.21547 1.40973 2.5918 1.21799C3.01962 1 3.58009 1 4.7002 1H16.3002C17.4203 1 17.9796 1 18.4074 1.21799C18.7837 1.40973 19.0905 1.71547 19.2822 2.0918C19.5 2.5192 19.5 3.07899 19.5 4.19691V9.80309C19.5 10.921 19.5 11.48 19.2822 11.9074C19.0905 12.2837 18.7841 12.5905 18.4077 12.7822C17.9803 13 17.421 13 16.3031 13H16.2529M14.5 16H6.5L10.5 11L14.5 16Z" stroke="#673391" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
        `;
        cueCardButton.html(svgCueCardIcon);
        cueCardButton.append(cueCardText);

        // Create the "Step by Step Video" button
        const stepByStepText = $('<span>', { class: 'step-by-step-text', text: 'Step by Step Video' });

        // Add the SVG icon and text to the "Step by Step Video" button
        const svgStepByStepIcon = `
            <svg width="16" height="18" viewBox="0 0 16 18" fill="none" xmlns="http://www.w3.org/2000/svg">
                <path d="M1 14.3336V3.66698C1 2.78742 1 2.34715 1.18509 2.08691C1.34664 1.85977 1.59564 1.71064 1.87207 1.67499C2.18868 1.63415 2.57701 1.84126 3.35254 2.25487L13.3525 7.58821L13.3562 7.58982C14.2132 8.04692 14.642 8.27557 14.7826 8.58033C14.9053 8.84619 14.9053 9.15308 14.7826 9.41894C14.6418 9.72413 14.212 9.95371 13.3525 10.4121L3.35254 15.7454C2.57645 16.1593 2.1888 16.3657 1.87207 16.3248C1.59564 16.2891 1.34664 16.1401 1.18509 15.9129C1 15.6527 1 15.2132 1 14.3336Z" stroke="#673391" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
        `;
        stepByStepVideo.html(svgStepByStepIcon);
        stepByStepVideo.append(stepByStepText);
    
        // Append the buttons to the buttons container
        const buttonsContainer = $('<div>', { class: 'buttons-container' });
        buttonsContainer.append(cueCardButton, stepByStepVideo, useExistingTemplate);
    
        // Append stepper container and button container to the wrapper
        wrapperDiv.append(stepperContainer, buttonsContainer);
    
        // Finally, append the wrapper div to the specific template row in the DOM
        $('#templateTr td').html('').append(wrapperDiv);

        // Add event listeners to buttons
        cueCardButton.on('click', function () {
            console.log("Cue Card clicked");
            // Add Cue Card functionality here
        });

        stepByStepVideo.on('click', function () {
            console.log("Step by Step Video clicked");
            step_by_step();  // Use existing step_by_step() function
        });

        
        useExistingTemplate.on('click', function () {
            console.log("Use Existing Template clicked");
            template_IssueLc();  // Use existing template_IssueLc() function
        });

        // Add event listener for the Next button
         $('.next-button').on('click', function () {
            handleNextButtonClick();
        });

		// Initial call to check the stepper state on page load
		updateStepperProgress();
	}

	function handleStepClick(containerId, stepDiv, section, newSectionId) {
        const elem = containerId[0];
        const $stepDiv = $(stepDiv);
        
        // Get the panel ID using the 'data-target' attribute from the first element in containerId
        var panelId = elem.getAttribute('data-target');
        var panel = document.getElementById(panelId); // Access panel using the data-target
        
        if ($stepDiv.hasClass('active')) {
            // If the clicked step is already active, hide everything
            hideall(elem);  // Hide the section content
            $stepDiv.removeClass('active').addClass('inactive');  // Mark step as inactive
            activeStep = null;  // No active step now
        } else {
            // First, hide all sections and reset all steps
            hideall(elem);
            
            // Show the section content for the clicked step
            callMe(elem);  // This should make the clicked step visible and active
            
            // Deactivate the previously active step if there is any
            if (activeStep) {
                $(activeStep).removeClass('active').addClass('inactive'); // Reset previous active step
            }
            
            // Activate the clicked step
            $stepDiv.removeClass('inactive').addClass('active');
            
            // Manage section visibility
            const sectionElement = document.getElementById(newSectionId);
            if (sectionElement) {
                // Hide all other sections and activate the current one
                $('.section-content').removeClass('active').addClass('inactive');
                sectionElement.classList.remove('inactive');
                sectionElement.classList.add('active'); // Show only the current section
            }
    
            // Set the clicked step as the new active step
            activeStep = stepDiv;
            
            // Deactivate all steps that come after the clicked step
            deactivateFollowingSteps(section.number);
            
            // Check mandatory fields and update the stepper icon or state accordingly
            checkMandatoryFields(section, section.number);
        }
    }
    
    // Function to deactivate all steps that come after the clicked step
    function deactivateFollowingSteps(clickedStepNumber) {
        sections.forEach(section => {
            // Deactivate steps whose number is greater than the clicked step's number
            if (section.number > clickedStepNumber) {
                const stepDiv = $(`#step_${section.divid}`);
                stepDiv.removeClass('active').addClass('inactive');  // Mark the step as inactive
            }
        });
    }
    

	// Function to check mandatory fields for each section and update the stepper accordingly
	function checkMandatoryFields(section,number) {
		const div = $(`#${section.divid}`);
		const stepIcon = $(`#step_${section.divid} .step-number`); // Use the step number element as the icon
		let allFilled = true;

		// Check if all required fields are filled in the section
		div.find('.DATE_M, .CHAR_M, .INT_M, .AMT_M').each(function () {
			if ($(this).val() === "") {
				allFilled = false;
			}
		});

		// Update the step icon based on the completion status
		if(SYS_FUNCTION_TYPE != 'RE'){
				if (allFilled) {
					//debugger;
				 stepIcon.html(`
                 <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <rect width="24" height="24" rx="12" fill="#5553AA"/>
                    <path d="M9.74998 15.1275L7.14748 12.525C6.85498 12.2325 6.38248 12.2325 6.08998 12.525C5.79748 12.8175 5.79748 13.29 6.08998 13.5825L9.22498 16.7175C9.51748 17.01 9.98998 17.01 10.2825 16.7175L18.2175 8.78249C18.51 8.48999 18.51 8.01749 18.2175 7.72499C17.925 7.43249 17.4525 7.43249 17.16 7.72499L9.74998 15.1275Z" fill="white"/>
					</svg>
				`);
				} else {
            // Reset to step number and use SVG text based on section.number
				stepIcon.html(`
					<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
						<text x="12" y="16" font-family="Roboto" font-size="14" font-weight="400" 
							fill="var(--icon-color, #FFF)" text-anchor="middle" 
							style="line-height: 28px; text-align: center;">
							${section.number}
						</text>
					</svg>
				`);
			}
           
        }else if(SYS_FUNCTION_TYPE == 'RE'){
				 stepIcon.html(`
                 <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <rect width="24" height="24" rx="12" fill="#5553AA"/>
                    <path d="M9.74998 15.1275L7.14748 12.525C6.85498 12.2325 6.38248 12.2325 6.08998 12.525C5.79748 12.8175 5.79748 13.29 6.08998 13.5825L9.22498 16.7175C9.51748 17.01 9.98998 17.01 10.2825 16.7175L18.2175 8.78249C18.51 8.48999 18.51 8.01749 18.2175 7.72499C17.925 7.43249 17.4525 7.43249 17.16 7.72499L9.74998 15.1275Z" fill="white"/>
					</svg>
				`);
			}

	}

	// Function to update the stepper based on form progress
	function updateStepperProgress() {
		sections.forEach(section => {
			checkMandatoryFields(section);
		});
	}

	// Attach change event listeners to inputs to trigger stepper updates
	$('input, select, textarea').on('change keyup', updateStepperProgress);

	// Initialize the stepper on page load
	initializeStepper();
    

    sections.forEach(section => {
        if (!section.divid) {
            console.error(`section.divid is undefined for section`, section);
        }
		//debugger;

        const panelDiv = $(`#${section.divid}`).parent('div');
        console.log("Panel div HTML:", panelDiv);
        
        const sectionHtml = `
            <div id="${section.id}_container" style="display: flex; align-items: center; justify-content: space-between; padding: 20px 20px 20px 20px;" onclick="callMe(this)"  data-target="${section.id}_panel">
                <div style="align-items: center;">
                    <span id="${section.numberId}" class="number-circle">${section.number}</span>
                    <!-- Replacing tick image with inline SVG -->
                    <svg id="${section.tickId}" class="tick-icon" style="display: none;" width="32" height="32" viewBox="0 0 32 32" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <rect width="32" height="32" rx="16" fill="#5553AA"/>
                        <path d="M13.75 19.1275L11.1475 16.525C10.855 16.2325 10.3825 16.2325 10.09 16.525C9.79748 16.8175 9.79748 17.29 10.09 17.5825L13.225 20.7175C13.5175 21.01 13.99 21.01 14.2825 20.7175L22.2175 12.7825C22.51 12.49 22.51 12.0175 22.2175 11.725C21.925 11.4325 21.4525 11.4325 21.16 11.725L13.75 19.1275Z" fill="white"/>
                    </svg>
                    <span class="section-title">${section.title}</span>
                    <br/>
                    <span id="${section.id}_warning" style="color: #747474; font-family: Roboto-Regular; font-size: 12px; font-weight: 400; line-height: 24px; display: none; margin-left: 45px;">
                        Fields marked with an asterisk (*) are mandatory.
                    </span>
                </div>
                <div style="display: flex; align-items: center;">
                    <img src="/CEWeb/Images/icons/caret-up.svg" id="${section.id}_UpArrow" class="arrow-icon" style="display:none;" />
                    <img src="/CEWeb/Images/icons/caret-down.svg" id="${section.id}_DownArrow" class="arrow-icon down" style="display:block;" />
                </div>
            </div>
            <hr id="${section.id}_separator" style="display: none; margin-top: -20px;
    border-width: 1px 0px 0px;
    border-right-style: initial;
    border-bottom-style: initial;
    border-left-style: initial;
    border-right-color: initial;
    border-bottom-color: initial;
    border-left-color: initial;
    border-image: initial;
    border-top-style: solid;
    border-top-color: rgb(224, 224, 224);
    margin-left: 20px;
    margin-right: 20px;
    ">
        `;
    
        // Append the generated HTML to the section
        $(`#${section.id}`).html(sectionHtml);
        // Now, insert the panelDiv before the separator
        $(`#${section.id}_separator`).after(panelDiv);

        $(`#${section.id}_panel`).css({           
            "padding": "20px"
        });
        

    }); 


	// Attach the click event to toggle arrows, visibility, and warning message using event delegation
	sections.forEach(section => {
       // debugger;
		$(document).on('click', `#${section.id}_container`, function () {
			//alert(`Section ID: ${section.id}`);

			// Toggle arrow visibility
			const isDownArrowVisible = $(`#${section.id}_DownArrow`).is(':visible');
			
			if (isDownArrowVisible) {
				// If the up arrow is visible, we are collapsing the section
				$(`#${section.id}_UpArrow`).hide();
				$(`#${section.id}_DownArrow`).show();

				// Hide the warning message and the separator
				$(`#${section.id}_warning`).hide();
				$(`#${section.id}_separator`).hide();
			} else {
				
				// If the down arrow is visible, we are expanding the section
				$(`#${section.id}_DownArrow`).hide();
				$(`#${section.id}_UpArrow`).show();

				// Show the warning message and the separator
				$(`#${section.id}_warning`).show();
				$(`#${section.id}_separator`).show();
			}
		});
	});



    // Function to check mandatory fields for each section
    function checkMandatoryFieldsForSections() {
        sections.forEach(section => {
            let allFieldsFilled = true;

            const div = $(`#${section.divid}`);

            div.find('.DATE_M, .CHAR_M, .INT_M, .AMT_M').each(function () {
                if ($(this).val() === "") {
                    allFieldsFilled = false;
                    $(`#${section.id}_warning`).show(); // Show warning if any field is empty
                } else {
                    $(`#${section.id}_warning`).hide(); // Hide warning if all fields are filled
                    div.find('.number-circle').hide();
                }
            });

            // Show or hide the tick icon based on the validation result
            if (allFieldsFilled) {
                $(`#${section.tickId}`).show(); // Show the tick icon
                $(`#${section.numberId}`).hide();
            } else {
                $(`#${section.tickId}`).hide(); // Hide the tick icon
                $(`#${section.numberId}`).show();
            }
			
				
        });
    }

    // Call this function to check mandatory fields whenever relevant (e.g., on input change)
    $('input, select, textarea').on('change', checkMandatoryFieldsForSections);
  

    function renderTrxSysBtnsDiv1() {
        // Create the div container
        var $trxSysBtnsDiv = $('<div>', { id: 'TrxSysBtnsDiv1', class: 'button-container' });

        // Create the Save as Draft button
        var $saveButton = $('<input>', {
            type: 'button',
            id: 'prim_save',
            class: 'prim_save',
            value: 'Save as Draft',
            click: function () {
                saveAsDraft();
            }
        });

        // Create the Create Template button
        var $createTemplateButton = $('<input>', {
            type: 'button',
            id: 'TEMP_BT',
            class: 'prim_save',
            value: 'Create Template',
            click: function () {
                if (openTabCreateTemplate()) {
                    AssignTemplateValue('C');
                    Template();
                } else {
                    return false;
                }
            }
        });

        // Create the Cancel button
        var $cancelButton = $('<input>', {
            type: 'button',
            id: 'sec_cancel',
            class: 'sec_cancel',
            value: 'Cancel',
            click: function () {
                CancelTransaction();
            }
        });

        // Create the Next and Submit buttons
        var $nextButton = $('<input>', {
            type: 'button',
            id: 'NEXT_BT',
            class: 'next-button',
            value: 'Next',
            click: function () {
                handleNextButtonClick();
            }
        });

      
		
		var $prim_confirm = $('<input>', {
            type: 'button',
            id: 'PRE_BT',
            class: 'prim_next',
            value: 'Preview & Submit',
            style: 'display: none;', // Initially hidden
            click: function () {
               // submitTab();
			   ConfirmTransaction();
            }
        });

        // Append the buttons to the container
        $trxSysBtnsDiv.append($saveButton,$cancelButton, $nextButton, $submitButton);

        // Replace the existing TrxSysBtnsDiv1 with the new one
        $('#TrxSysBtnsDiv1').replaceWith($trxSysBtnsDiv);
    }

    // Call the function to render the button div
    if(SYS_FUNCTION_TYPE != "RE"){
        renderTrxSysBtnsDiv1();
    }

    // Function to handle the Next button click
    // Function to handle the Next button click

    function handleNextButtonClick() {
        let currentSection = sections[currentStepIndex];
        let currentStep = $(`#step_${currentSection.divid}`);
        hideall(currentStep[0]);
        if (currentStepIndex < sections.length - 1) {
            currentStepIndex++;
            let nextSection = sections[currentStepIndex];
            let nextStep = $(`#step_${nextSection.divid}`);
            callMe(nextStep[0]); 
            nextStep.trigger('click');
            let firstInput = nextStep.find('input, select, textarea').first();
            if (firstInput.length) {
                firstInput.focus(); // Set focus to the first input element of the new section
            }
        } else {
            console.log("You are on the last step.");
            $(`#step_${currentSection.divid}`).find('.DATE_M, .CHAR_M, .INT_M, .AMT_M').on('change keyup', function () {
                checkMandatoryFieldsForDiv(currentSection.divid);             
                checkAllMandatoryFields(); 
            });
            checkAllMandatoryFields();
        }
    }
    InitFldEvt();

    $(".DATE_M, .DATE_O").each(function() {
        var $this = $(this);
    
        // Initialize AirDatepicker with all relevant callbacks
        new AirDatepicker(this, {
            locale: {
                days: ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'],
                daysShort: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'],
                daysMin: ['Su', 'Mo', 'Tu', 'We', 'Th', 'Fr', 'Sa'],
                months: ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'],
                monthsShort: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'],
                today: 'Today',
                clear: 'Clear',
                dateFormat: 'dd/MM/yyyy',
                firstDay: 0
            },
            dateFormat: 'dd/MM/yyyy',
            view: 'days',
            minView: 'days',
            autoClose: true,
    
            // Callback when a date is selected
            onSelect: function(formattedDate, date, inst) {
                console.log($this.attr('id') + ' date selected: ' + formattedDate);
                // Manually trigger the onchange event after date selection
                $this.trigger('change');
    
                // Optionally call custom onchange function
                var changeFunctionName = $this.data('onchange'); // Get function name from data attribute
                if (changeFunctionName && typeof window[changeFunctionName] === 'function') {
                    window[changeFunctionName](); // Call the function dynamically
                }
            },
    
            // Callback when datepicker is shown
            onShow: function(inst) {
                console.log($this.attr('id') + ' datepicker shown');
                $this.trigger('focus'); // Trigger focus event when datepicker is shown
            },
    
            // Callback when datepicker is hidden
            onHide: function(inst) {
                console.log($this.attr('id') + ' datepicker hidden');
                $this.trigger('blur'); // Trigger blur event when datepicker is hidden
            },
    
            // Callback when datepicker is rendered
            onRender: function(inst) {
                console.log($this.attr('id') + ' datepicker rendered');
            }
        });

        // Hide the jQuery UI Datepicker div if still present
        $("#ui-datepicker-div").remove(); // Remove the lingering jQuery UI element
    
        // Mark as initialized
        $this.addClass('air-datepicker-initialized');

        // Ensure AirDatepicker is not re-attached multiple times
        if ($this.hasClass('air-datepicker-initialized')) {
            return;
        }

        // Remove any lingering jQuery UI Datepicker
        if ($this.hasClass("hasDatepicker")) {
            $.datepicker._showDatepicker = function() {
                return false; // Block any further jQuery UI Datepicker from appearing
            };
        }

       

    
        // Re-attach existing events
        addOnChange($this.attr('id'));
        addOnBlur($this.attr('id'));
        addOnClick($this.attr('id'));
        addOnFocus($this.attr('id'));
    
        // Optionally bind custom onblur, onclick, onfocus events
        $this.on('blur', function() {
            console.log($this.attr('id') + ' field blurred');
            // Call custom blur function if needed
            var blurFunctionName = $this.data('onblur');
            if (blurFunctionName && typeof window[blurFunctionName] === 'function') {
                window[blurFunctionName]();
            }
        });
    
        $this.on('focus', function() {
            console.log($this.attr('id') + ' field focused');
            // Call custom focus function if needed
            var focusFunctionName = $this.data('onfocus');
            if (focusFunctionName && typeof window[focusFunctionName] === 'function') {
                window[focusFunctionName]();
            }
        });
    
        $this.on('click', function() {
            console.log($this.attr('id') + ' field clicked');
            // Call custom click function if needed
            var clickFunctionName = $this.data('onclick');
            if (clickFunctionName && typeof window[clickFunctionName] === 'function') {
                window[clickFunctionName]();
            }
        });
    });
if(SYS_FUNCTION_TYPE == "RE"){
		//debugger;
		 $('#number').hide(); // Hide the tick icon
         $('#number1').hide();
		 $('#number2').hide();
		 $('#number3').hide();
		 $('#number4').hide();
		 $('#number5').hide();
		 $('#number6').hide();
		 $('#tick1').show();
		 $('#tick2').show();
		 $('#tick3').show();
		 $('#tick4').show();
		 $('#tick5').show();
		 $('#tick6').show();
	}
	
	function updateButtonStyles(buttonIdToSelect, otherButtonIds) {
        const selectedButton = $(`#${buttonIdToSelect}`);
        console.log(`Before removal: ${selectedButton.attr('class')}`);  
   
        selectedButton.addClass('BORDER_BOTTOM_SELECTED')
                      .removeAttr('style')
                      .css({
                        'background-color': 'white',
                        'color': '#5b1d8d',
                        'width': '33.33333%'
                        });
       
        console.log(`After removal: ${selectedButton.attr('class')}`);  

        otherButtonIds.forEach(buttonId => {
            $(`#${buttonId}`).removeClass('BORDER_BOTTOM_SELECTED').addClass('BORDER_RIGHT_WHITE').removeAttr('style');
        });
    }
   
    setTimeout(() => {
        if (sSelectedFuncId == "F05030701145") {
            updateButtonStyles('viewLCButton', ['unlockLCButton', 'authLCButton']);
        } else if (sSelectedFuncId == "F05030701029") {
            updateButtonStyles('authLCButton', ['unlockLCButton', 'viewLCButton1']);
        }
    }, 1000);
});




 
   
   
   
