function validateEmail(email) {
  const validDomains = ['.com', '.net', '.co.in', '.gov.in'];

  // Rule 1: Must contain exactly one '@'
  if (!email.includes('@') || email.indexOf('@') !== email.lastIndexOf('@')) {
    alert('Please enter a valid email address.');
    return false;
  }

  const [localPart, domainPart] = email.split('@');

  // Rule 4: No spaces
  if (/\s/.test(email)) {
    alert('Please enter a valid email address.');
    return false;
  }

  // Rule 2 & 6: Start and end should not be special characters
  const specialChars = '-_+=.';
  if (
    specialChars.includes(email[0]) ||
    specialChars.includes(email[email.length - 1])
  ) {
    alert('Please enter a valid email address.');
    return false;
  }

  // Rule 5: Check allowed characters before @
  const localValid = /^[A-Za-z0-9\-\_\+\=\.]+$/;
  if (!localValid.test(localPart)) {
    alert('Please enter a valid email address.');
    return false;
  }

  // Rule 3: Domain must end with allowed domains
  const isValidDomain = validDomains.some((domain) => domainPart.endsWith(domain));
  if (!isValidDomain) {
    alert('Please enter a valid email address.');
    return false;
  }

  return true;
}
