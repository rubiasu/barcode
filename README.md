# Barcode Management System

This project is a barcode management application built with Svelte. It allows users to load, add, remove, and save barcodes and associate them with names and sections. Designed with client-side operations in mind, all data is processed and stored locally within the user's browser. This tool can be particularly useful in logistics, inventory management, or any scenario where barcode tracking is necessary without the need for server-side data handling.

## Features

- **Load Barcodes**: Import barcode data from a JSON file.
- **Add Barcodes**: Manually enter barcode details and add them to the system.
- **Remove Barcodes**: Remove a selected barcode entry.
- **Save Barcodes**: Export the current list of barcodes to a JSON file.
- **Copy Barcodes**: Copy barcode text to the clipboard for external use.
- **Filter Barcodes**: View barcodes by their assigned sections.
- **Demo Mode**: Populate the application with sample data to explore features.

## Getting Started

### Prerequisites

- Node.js

### Installation

1. Clone the repository:
```
git clone https://github.com/rubiasu/barcode.git
```
2. Navigate to the project directory:

```
cd your-repo-name
```

3. Install dependencies:

```
npm install
```

### Running the Application

1. Start the development server:

```
npm run dev
```

2. Visit http://localhost:5173 in your web browser.

## Usage
### Adding a Barcode

- Click the "Add Barcode" button.
- Fill in the barcode details in the form.
- Submit the form to add the barcode to the list.

### Removing a Barcode
- Toggle the "Remove Barcode" mode.
- Click the "Remove" button next to the barcode you want to delete.

### Saving and Loading Barcodes

- Click "Save Barcodes" to download your data as a JSON file.
- Use the file input to upload and load barcode data from a JSON file.

### Contributing

Please feel free to contribute to the improvement of this project by submitting issues or pull requests.