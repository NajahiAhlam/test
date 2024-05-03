 interne: number = 123;
  allocated: number = 1000;
  engaged: number = 800;
  target1: number = 1200;
  consumed: number = 600;
  target2: number = 1000;
  billed: number = 700;
  target3: number = 1500;

  updateTables(): void {
    // Update data for the first table
    this.updateFirstTableData();
    // Update data for the second table
    this.updateSecondTableData();
    // Update data for the third table
    this.updateThirdTableData();
  }


  updateFirstTableData(): void {
    // Update your data variables here based on your requirements
    this.interne = this.generateRandomNumber();
    // Update other data variables as needed
  }

  // Function to update data for the second table
  updateSecondTableData(): void {
    // Update your data variables here based on your requirements
    this.allocated = this.generateRandomNumber();
    this.engaged = this.generateRandomNumber();
    this.target1 = this.generateRandomNumber();
    this.consumed = this.generateRandomNumber();
    this.target2 = this.generateRandomNumber();
    this.billed = this.generateRandomNumber();
    this.target3 = this.generateRandomNumber();
    // Update other data variables as needed
  }

  // Function to update data for the third table
  updateThirdTableData(): void {
    // Update your data variables here based on your requirements
    // Update other data variables as needed
  }

  // Function to generate a random number
  generateRandomNumber(): number {
    return Math.floor(Math.random() * 1000); // Adjust range as needed
  }
