# Delphi Entities Validators

**Delphi Entities Validators** is a small framework that provides an easy and ready-to-use interface for entities validation.

## How it works

### Simple Validation

```pascal
TPersonValidator = class(TInterfacedObject, IValidator<TPerson>)
public
  function Validate(aEntity: TPerson): IValidationResult;
end;
  
implementation

{TPersonValidator}
  
function TPersonValidator.Validate(aEntity: TPerson): IValidationResult;
var
  lIsValid: boolean;
begin
  Result := TValidationResult.Create;
  lIsValid := not (aEntity.Firstname.IsEmpty //
      or aEntity.Lastname.IsEmpty //
      or aEntity.Email.IsEmpty);
  if not lIsValid then
    Result.BrokenRules := ['The fields Firstname, Lastname and Email are mandatory '];
end;

...

lPersonValidator := TPersonValidator.Create;
lValidationResult := lPersonValidator.Validate(lPerson);
// returns a boolean that represents the success or failures validation
lValidationResult.IsValid; 
// returns an array of string that represents the broken rules
lValidationResult.BrokenRules; 
```

### Attributes Validation

```pascal
TPerson = class
private
  FLastname: string;
  FFirstname: string;
  procedure SetFirstname(const Value: string);
  procedure SetLastname(const Value: string);
public
  [RequiredValidation('AttributesValidation', 'Firstname is required')]
  [MaxLengthValidation('AttributesValidation', 'Firstname is too long', 8)]
  [MinLengthValidation('AttributesValidation', 'Firstname is too short', 4)]
  property Firstname: string read FFirstname write SetFirstname;
  
  RequiredValidation('AttributesValidation', 'Lastname is required')]
  property Lastname: string read FLastname write SetLastname;
end;
  
...
  
lValidationResult := TValidationEngine.PropertyValidation(lPerson, 'AttributesValidation');
// returns a boolean that represents the success or failures validation
lValidationResult.IsValid; 
// returns an array of string that represents the broken rules 
lValidationResult.BrokenRules; 
```
### Using container

```pascal
TPersonLoginValidator = class(TInterfacedObject, IValidator<TPerson>)
public
  function Validate(aEntity: TPerson): IValidationResult;
end;
  
implementation
  
function TPersonLoginValidator.Validate(aEntity: TPerson): IValidationResult;
var
  lIsValid: boolean;
begin
  Result := TValidationResult.Create;
  lIsValid := not (aEntity.Firstname.IsEmpty 
       or aEntity.Lastname.IsEmpty 
       or aEntity.Email.IsEmpty);
  if not lIsValid then
    Result.BrokenRules := ['The fields Firstname, Lastname and Email are mandatory '];
end;

...

TValidationEngine.ValidationContainer.RegisterValidatorFor<TPerson>('PersonLogidValidation', TPersonLoginValidator.Create);
lValidationResult := TValidationEngine.EntityValidation<TPerson>(lPerson,'PersonLoginValidation');
// returns a boolean that represents the success or failures validation
lValidationResult.IsValid; 
// returns an array of string that represents the broken rules
lValidationResult.BrokenRules; 
```