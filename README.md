getHighlightedNames(names: string, activeName: string): string {
  if (!names) return '';

  const nameArray = names.split(',').map(n => n.trim()).filter(n => !!n);

  // Count occurrences
  const countMap = new Map<string, number>();
  nameArray.forEach(name => {
    countMap.set(name, (countMap.get(name) || 0) + 1);
  });

  // Build unique display list with counts and highlighting
  const result = Array.from(countMap.entries()).map(([name, count]) => {
    const isActive = name === activeName;
    const display = count > 1 ? `${name} ×${count}` : name;

    return isActive
      ? `<span style="color: green; font-weight: bold;">${display}</span>`
      : display;
  });

  return result.join(', ');
}
