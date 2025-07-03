function getDaysBetweenDates(date1, date2) {
  const d1 = new Date(date1);
  const d2 = new Date(date2);

  const timeDiff = Math.abs(d2 - d1); // Difference in milliseconds
  const dayDiff = Math.ceil(timeDiff / (1000 * 60 * 60 * 24)); // Convert to days

  return dayDiff;
}

// Example usage:
console.log(getDaysBetweenDates("2025-07-01", "2025-07-03")); // Output: 2
