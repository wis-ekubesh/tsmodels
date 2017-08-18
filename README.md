# tsmodels
Implementation of the model system for the TypeScript language

## Features
- Convert format fields from server to client and back
- Creates instances of models rather than typing (with conversion)
- Perceives both nested (connected) models and collections of any depth

## Installation
Install `tsmodels` from `npm` or `yarn`:
```bash
# from `npm`
npm i tsmodels --save

# from `yarn`
yarn add tsmodels --save
```

## Usage
It is necessary to inherit the model from the class `ApplicationModel`:
```typescript
import { Alias, ApplicationModel } from 'tsmodels';

export class User extends ApplicationModel {
  // ...
}
```
For the model instance, methods will be available `_fromJSON(value)` and `_toJSON()` methods

### @Alias(...)
Decorator of fields for model

- Optional param `field_name` - Name of server format field
- Optional param `type` - Optional. Type of related model

## Example

##### Town model
```typescript
// ...

export class Town extends ApplicationModel {
  @Alias('main_title') public mainTitle: string;
}
```

##### User model
```typescript
// ...

export class User extends ApplicationModel {
  @Alias() public name: string;
  @Alias('last_name') public lastName: string;
  @Alias('towns', Town) public towns: Town[];
  @Alias('main_town', Town) public townMain: Town;
}
```

```typescript
// ...

const townMock = { main_title: 'Sulonia' };
const townMock2 = { main_title: 'Sulon' };

const userMock = {
  name: 'Kyle Katarn',
  last_name: 'Katarn',
  towns: [townMock, townMock2],
  main_town: [townMock2],
};

class ModelExample {
  public user: User = new User();
  
  constructor() {
    this.user._fromJSON(userMock);
    this.user._toJSON();
  }
}
```

##### _fromJSON()
User will be:
```json
{
  "name": "Kyle Katarn", 
  "lastName": "Katarn", 
  "towns": [
    {
      "mainTitle": "Sulonia"
    },
    {
      "mainTitle": "Sulon"
    }
  ],
  "townMain": {
    "mainTitle": "Suloniaaaa3"
  }
}
```

##### _toJSON()
User will be:
```json
{
  "name": "Kyle Katarn", 
  "last_name": "Katarn", 
  "towns": [
    {
      "main_title": "Sulonia"
    },
    {
      "main_title": "Sulon"
    }
  ],
  "main_town": {
    "main_title": "Suloniaaaa3"
  }
}
```