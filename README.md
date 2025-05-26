@ViewChild('fileInput') fileInput!: ElementRef<HTMLInputElement>;
  if (this.fileInput) {
    this.fileInput.nativeElement.value = ''; // ✅ Clear the file input field
  }
