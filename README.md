const match = data.content.find(d => d.typeDemande === 'Nouveau Demande');
if (match) {
  data.content = [match];
} else {
  // Handle gracefully (e.g., keep existing, or set a default)
  data.content = [data.content[0]];
}
