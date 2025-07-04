<!DOCTYPE html>
<html>
<head>
  <title>Read File and Extract IDs</title>
</head>
<body>
  <h2>Select HTML file:</h2>
  <input type="file" id="fileInput" />

  <script>
    document.getElementById('fileInput').addEventListener('change', function (e) {
      const file = e.target.files[0];
      if (!file) return;

      const reader = new FileReader();

      reader.onload = function (event) {
        const content = event.target.result;

        // Parse the HTML content
        const parser = new DOMParser();
        const doc = parser.parseFromString(content, "text/html");

        // Get all elements with id and name, and not type=hidden
        const elements = doc.querySelectorAll('[id][name]');
        const validIds = [];

        elements.forEach(el => {
          const type = el.getAttribute('type');
          if (!type || type.toLowerCase() !== 'hidden') {
            validIds.push(el.id);
          }
        });

        console.log("Valid IDs:", validIds);
        alert("IDs found:\n" + validIds.join('\n'));
      };

      reader.readAsText(file);
    });
  </script>
</body>
</html>
