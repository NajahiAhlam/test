getHighlightedNames(names: string, activeName: string): string {
  if (!names || !activeName) return '';

  const cleanedActiveName = activeName.trim().toLowerCase();

  const nameArray = names
    .split(',')
    .map(n => n.trim())
    .filter(n => !!n);

  const countMap = new Map<string, number>();
  nameArray.forEach(name => {
    const trimmedName = name.trim();
    countMap.set(trimmedName, (countMap.get(trimmedName) || 0) + 1);
  });

  const result = Array.from(countMap.entries()).map(([name, count]) => {
    const normalized = name.trim().toLowerCase();
    const isActive = normalized === cleanedActiveName;
    const display = count > 1 ? `${name} ×${count}` : name;

    return isActive
      ? `<span style="color: green; font-weight: bold;">${display}</span>`
      : display;
  });

  return result.join(', ');
}
