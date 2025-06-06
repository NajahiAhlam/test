No overload matches this call.
  Overload 1 of 2, '(observerOrNext?: Partial<Observer<string | null>> | ((value: string | null) => void) | undefined): Subscription | undefined', gave the following error.
    Argument of type '(value: string) => void' is not assignable to parameter of type 'Partial<Observer<string | null>> | ((value: string | null) => void) | undefined'.
      Type '(value: string) => void' is not assignable to type '(value: string | null) => void'.
        Types of parameters 'value' and 'value' are incompatible.
          Type 'string | null' is not assignable to type 'string'.
            Type 'null' is not assignable to type 'string'.
  Overload 2 of 2, '(next?: ((value: string | null) => void) | null | undefined, error?: ((error: any) => void) | null | undefined, complete?: (() => void) | null | undefined): Subscription | undefined', gave the following error.
    Argument of type '(value: string) => void' is not assignable to parameter of type '(value: string | null) => void'.
      Types of parameters 'value' and 'value' are incompatible.
      (value: string) => {
