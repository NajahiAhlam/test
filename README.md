exportToCSV(): void {
  const headers = this.columns.map(col => col.label);
  const keys = this.columns.map(col => col.key);

  const csvRows = [
    headers.join(','), // header row
    ...this.filteredData.map(row => {
      return keys.map(key => {
        let cell = row[key];

        // Format pipes or functions
        if (key === 'validateur' || key === 'assigneeName') {
          cell = this.formatEmail(cell); // Example: custom formatting
        } else if (key === 'productStatus') {
          cell = this.getActionLabel(cell); // Your custom label
        } else if (key === 'categorie') {
          cell = this.getCategorieClass(cell); // Your custom label
        }

        return `"${(cell ?? '').toString().replace(/"/g, '""')}"`; // escape quotes
      }).join(',');
    })
  ];

  const csvContent = csvRows.join('\r\n');
  const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });

  const link = document.createElement('a');
  const url = URL.createObjectURL(blob);
  link.setAttribute('href', url);
  link.setAttribute('download', 'report.csv');
  link.click();
  URL.revokeObjectURL(url);
}

// Example helper function
formatEmail(email: string): string {
  if (!email) return '';
  return email.split('@')[0].replace('.', ' ').toUpperCase(); // or use your pipe logic
}
formatEmail(email: string): string {
  if (!email) return '';
  const [beforeAt] = email.split('@');
  const [first, last] = beforeAt.split('.');
  if (!first || !last) return email;
  return `${this.capitalize(first)} ${this.capitalize(last)}`;
}

capitalize(word: string): string {
  return word.charAt(0).toUpperCase() + word.slice(1).toLowerCase();
}
