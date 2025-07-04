<!DOCTYPE html>
<html>
<head>
  <title>Read HTML File and Extract IDs</title>
</head>
<body>
  <h3>Select an HTML file:</h3>
  <input type="file" id="fileInput" accept=".html,.htm">

  <script>
    document.getElementById("fileInput").addEventListener("change", function (e) {
      const file = e.target.files[0];

      if (!file) {
        alert("No file selected.");
        return;
      }

      const reader = new FileReader();

      reader.onload = function (event) {
        const html = event.target.result;

        // Parse the HTML
        const parser = new DOMParser();
        const doc = parser.parseFromString(html, "text/html");

        // Find elements with both id and name, and type is not hidden
        const elements = doc.querySelectorAll("[id][name]");
        const validIds = [];

        elements.forEach((el) => {
          const type = el.getAttribute("type");
          if (!type || type.toLowerCase() !== "hidden") {
            validIds.push(el.id);
          }
        });

        console.log("Valid IDs found:", validIds);
        alert("IDs found:\n" + validIds.join("\n"));
      };

      reader.onerror = function () {
        alert("Error reading file.");
        console.error(reader.error);
      };

      reader.readAsText(file); // Read file as text
    });
  </script>
</body>
</html>
