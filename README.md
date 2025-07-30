function validateAmount(input) {
  let val = input.value;

  // Remove invalid characters (only digits and one dot allowed)
  val = val.replace(/[^0-9.]/g, '');

  // Allow only one decimal point
  const parts = val.split('.');
  if (parts.length > 2) {
    val = parts[0] + '.' + parts[1]; // keep only first decimal
  }

  if (val.includes('.')) {
    // Limit digits before and after decimal
    const beforeDot = parts[0].slice(0, 12);
    const afterDot = parts[1] ? parts[1].slice(0, 2) : '';
    val = beforeDot + '.' + afterDot.padEnd(2, '0');
  } else {
    // No decimal, so add .00
    val = val.slice(0, 12) + '.00';
  }

  // Enforce total length of 15
  if (val.length > 15) {
    val = val.slice(0, 15);
  }

  input.value = val;
}
