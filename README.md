
# MyCurrency Project

MyCurrency is a web platform that allows users to calculate currency exchange rates. This project is designed to work with multiple currency data providers, and it includes features like exchange rate retrieval, and an admin interface for currency conversion views.

## Features

- Retrieve and store daily currency exchange rates.
- REST API endpoints to access exchange rates and perform currency conversions.
- Admin views for displaying exchange rate trends and conversion forms.

## Requirements

- Python 3.x
- Django 4.x

## Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/caarloscorral/mycurrency.git
   cd mycurrency
   ```

2. **Create a virtual environment:**
   ```bash
   python3 -m venv mycurrency-env
   source mycurrency-env/bin/activate  # On Windows use `mycurrency-env\Scripts\activate`
   ```

3. **Install the dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Set up the database:**
   ```bash
   python manage.py makemigrations exchange_rates
   python manage.py migrate exchange_rates
   ```

5. **Create a superuser for the admin interface:**
   ```bash
   python manage.py createsuperuser
   ```


   If the following error appears:
   ```bash 
   OperationalError: no such table: auth_user
   ```

   Run the following commands:
   ```bash 
   python manage.py migrate auth zero
   python manage.py migrate --fake-initial
   ```

6. **Run the development server:**
   ```bash
   python manage.py runserver
   ```

7. **Run Redis server:**
   - Ensure Redis is running as it acts as the broker for Celery tasks.
   ```bash
   redis-server
   ```

## Using the API

### Overview

The MyCurrency API provides several endpoints for managing exchange rates and converters:

1. **Change Provider Priority:**
   - **Endpoint**: `/change_provider_priority/`
   - **Description**: Change the priority of a currency exchange rate provider.
   - **Parameters**:
     - `provider`: Name of the provider (Default: 'Mock').
     - `new_priority`: New priority value (Default: 1).

2. **Exchange Rate Data:**
   - **Endpoint**: `/exchange_rate_data/`
   - **Description**: Retrieve exchange rate data for a given currency pair and date.
   - **Parameters**:
     - `source_currency`: Source currency code (Default: 'EUR').
     - `exchanged_currency`: Exchange currency code (Default: 'USD').
     - `valuation_date`: Date for the rate (Default: Today's date).
     - `provider`: Provider to use (Default: 'Mock').

3. **Rates List:**
   - **Endpoint**: `/rates_list/`
   - **Description**: List exchange rates over a period.
   - **Parameters**:
     - `source_currency`: Source currency code (Default: 'EUR').
     - `date_from`: Start date (Default: Yesterday's date).
     - `date_to`: End date (Default: Today's date).

4. **Latest Conversion:**
   - **Endpoint**: `/latest_conversion/`
   - **Description**: Convert an amount from one currency to another using the latest available rate.
   - **Parameters**:
     - `source_currency`: Source currency code (Default: 'EUR').
     - `amount`: Amount to convert (Default: 1).
     - `exchanged_currency`: Target currency (Default: 'USD').

5. **Time-Weighted Rate of Return (TWRR):**
   - **Endpoint**: `/twrr/`
   - **Description**: Calculate the Time-Weighted Rate of Return for an investment from a currency into another over a period.
   - **Parameters**:
     - `source_currency`: Source currency code (Default: 'EUR').
     - `amount`: Amount invested (Default: 1).
     - `exchanged_currency`: Target currency (Default: 'USD').
     - `start_date`: Start date of the investment (Default: Yesterday's date).


## Configuration

### Modifying Currencies

The application supports and displays several currencies, which are defined in the `exchange_rates/config.py` file. If you need to change, add, or remove currencies, you should modify the `CURRENCIES` dictionary in this file.

#### Example:

```python
CURRENCIES = {
    'EUR': {
        'name': 'Euro',
        'symbol': '€'
    },
    
    'CHF': {
        'name': 'Swiss Franc',
        'symbol': '₣'
    },
    
    'USD': {
        'name': 'United States Dollar',
        'symbol': '$'
    },
    
    'GBP': {
        'name': 'Great British Pound',
        'symbol': '£'
    },
}
```

To add a new currency, simply append it to the dictionary with its respective `name` and `symbol`.

### Setting API Keys for Providers

To support multiple data providers, the application allows you to set API keys in the `API_KEYS` dictionary in `exchange_rates/config.py`. This is particularly useful if you plan to integrate additional providers that require API authentication.

#### Example:

```python
API_KEYS = {
    'Fixer': 'API_KEY',
    'Mock': 'API_KEY'
}
```

If a new provider is added to the application, you should add an entry for it in the `API_KEYS` dictionary, replacing `'API_KEY'` with the actual key provided by the service.


## Data Import from CSV or JSON

The project includes a management command that allows you to import currency exchange rate data from a CSV or JSON file into the database. This can be especially useful for bulk data import or system initialization with existing data.

### Import Command Usage

To import data into the database, ensure your CSV or JSON file is structured appropriately and includes the required fields like `source_currency`, `exchanged_currency`, `valuation_date`, `rate_value`, and `provider`.

1. **Prepare Data File:**

   Ensure your data file is properly formatted. For example, a CSV file might look like this:

   ```csv
   source_currency,exchanged_currency,valuation_date,rate_value,provider
   EUR,USD,2024-10-08,1.1765,Mock
   EUR,GPB,2024-10-08,0.8547,Mock
   ```

   A JSON file would look like this:

   ```json
   [
       {"source_currency": "EUR", "exchanged_currency": "USD", "valuation_date": "2024-10-08", "rate": 1.1765, "provider": "Mock"},
       {"source_currency": "EUR", "exchanged_currency": "GBP", "valuation_date": "2024-10-08", "rate": 0.8547, "provider": "Mock"}
   ]
   ```

2. **Run the Import Command:**

   Use the custom Django management command to import the data:

   ```bash
   python manage.py ingest_rates <file_path> --file-type=<file_type>
   ```

   - `<file_path>`: Path to your CSV or JSON data file.
   - `<file_type>`: Type of file, either `csv` or `json`.

### Example:

```bash
python manage.py ingest_rates example_data/exchange_rates.csv --file-type=csv
python manage.py ingest_rates example_data/exchange_rates.json --file-type=json
```

Ensure that the correct file path is given and the server is running without issues to manage tasks synchronously.

## Troubleshooting

- Ensure all services like Redis and Celery are running.
- Check your environment variables and configurations in `settings.py` for any misconfigurations.

## Contributing

Contributions are welcome! Please fork the repository and submit a pull request for any enhancements, bug fixes, or documentation improvements.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.

### Testing the API

- Use tools like Postman or VSCode extension called Thunder Client to test endpoints by sending HTTP GET requests with the appropriate parameters.
- Ensure that your server is running when you send these requests to interact with the API effectively.

## Troubleshooting

- Check your environment variables and configurations in `settings.py` for any misconfigurations.

## Contributing

Contributions are welcome! Please fork the repository and submit a pull request for any enhancements, bug fixes, or documentation improvements.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details.
