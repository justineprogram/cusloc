<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Customer Location Saver</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 15px;
      background-color: #f2f2f2;
    }

    h2 {
      text-align: center;
      color: #333;
    }

    form {
      background-color: white;
      padding: 15px;
      border-radius: 8px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
      margin-bottom: 20px;
    }

    label {
      display: block;
      margin-bottom: 6px;
      font-weight: bold;
    }

    input[type="text"],
    input[type="tel"] {
      width: 100%;
      padding: 10px;
      margin-bottom: 15px;
      border-radius: 4px;
      border: 1px solid #ccc;
    }

    button {
      width: 100%;
      padding: 12px;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      font-size: 16px;
      cursor: pointer;
    }

    button:hover {
      background-color: #0056b3;
    }

    .search-box {
      margin-bottom: 20px;
    }

    .customer-list {
      list-style: none;
      padding: 0;
    }

    .customer-item {
      background-color: white;
      padding: 10px;
      margin-bottom: 10px;
      border-left: 5px solid #007bff;
      border-radius: 4px;
    }

    @media (max-width: 600px) {
      body {
        padding: 10px;
      }

      h2 {
        font-size: 20px;
      }
    }
  </style>
</head>
<body>
  <h2>Customer Location Tracker</h2>

  <form id="customerForm">
    <label for="name">Customer Name</label>
    <input type="text" id="name" required>

    <label for="contact">Customer Contact</label>
    <input type="tel" id="contact" required>

    <button type="submit">Save Customer Info + Location</button>
  </form>

  <div class="search-box">
    <input type="text" id="search" placeholder="Search by name or contact..." style="width:100%; padding:10px; border-radius:4px; border:1px solid #ccc;">
  </div>

  <ul class="customer-list" id="customerList"></ul>

  <script>
    const form = document.getElementById('customerForm');
    const customerList = document.getElementById('customerList');
    const searchInput = document.getElementById('search');

    let customers = [];

    form.addEventListener('submit', function(e) {
      e.preventDefault();

      const name = document.getElementById('name').value.trim();
      const contact = document.getElementById('contact').value.trim();

      if (!navigator.geolocation) {
        alert("Geolocation is not supported by your browser.");
        return;
      }

      navigator.geolocation.getCurrentPosition(position => {
        const { latitude, longitude } = position.coords;

        const customer = {
          name,
          contact,
          location: { latitude, longitude },
          timestamp: new Date().toLocaleString()
        };

        customers.push(customer);
        renderCustomers(customers);

        form.reset();
      }, () => {
        alert("Unable to retrieve location.");
      });
    });

    searchInput.addEventListener('input', () => {
      const query = searchInput.value.toLowerCase();
      const filtered = customers.filter(c =>
        c.name.toLowerCase().includes(query) ||
        c.contact.toLowerCase().includes(query)
      );
      renderCustomers(filtered);
    });

    function renderCustomers(list) {
      customerList.innerHTML = '';
      list.forEach((customer, index) => {
        const li = document.createElement('li');
        li.className = 'customer-item';
        li.innerHTML = `
          <strong>${customer.name}</strong><br>
          Contact: ${customer.contact}<br>
          Location: ${customer.location.latitude.toFixed(5)}, ${customer.location.longitude.toFixed(5)}<br>
          Saved: ${customer.timestamp}
        `;
        customerList.appendChild(li);
      });
    }
  </script>
</body>
</html>
