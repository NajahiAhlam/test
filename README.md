Argument of type '([assigneeName, items]: [string, any[]]) => any[]' is not assignable to parameter of type '(this: undefined, value: [string, unknown], index: number, array: [string, unknown][]) => any'.
  Types of parameters '__0' and 'value' are incompatible.
    Type '[string, unknown]' is not assignable to type '[string, any[]]'.
      Type at position 1 in source is not compatible with type at position 1 in target.
        Type 'unknown' is not assignable to type 'any[]'.ts(2345)
            flattenGroupedData(data: any): any[] {
    return Object.entries(data).flatMap(([assigneeName, items]: [string, any[]]) =>
      items.map(item => ({ ...item, assigneeName }))
    );
  }
