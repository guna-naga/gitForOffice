function getDaysBetweenDates(date1, date2) {
  // Helper to convert dd-mm-yyyy to Date
  function parseDate(str) {
    const [day, month, year] = str.split("-").map(Number);
    return new Date(year, month - 1, day); // Month is 0-based
  }

  const d1 = parseDate(date1);
  const d2 = parseDate(date2);

  const timeDiff = Math.abs(d2 - d1); // Difference in milliseconds
  const dayDiff = Math.ceil(timeDiff / (1000 * 60 * 60 * 24)); // Convert to days

  return dayDiff;
}

// Example usage:
console.log(getDaysBetweenDates("01-07-2025", "03-07-2025")); // Output: 2
