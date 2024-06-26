# Anchorjs

[![Patreon](https://img.shields.io/badge/Patreon-F96854?style=for-the-badge&logo=patreon&logoColor=white)](https://www.patreon.com/Pyriter)

Dependency injection for Nodejs.

## Install

```bash
npm install @pyriter/anchorjs
```

## Features

- Seamless imports with your IDE (via typescript)
- Allows named dependency
- Order of $install does not matter
- Infer dependency injection by object type

## Usage

Import the $install function to add objects, functions, values to the anchor module

```typescript
import { $install } from '@pyriter/anchorjs';

declare type Foo = {
  bar: string
};
const myValue: Foo = {
  bar: "foobar"
};

$install<Foo>("foo", {
  provide: () => myValue,
  type: DependencyType.SINGLETON,
});
```

In a separate module or file use the $inject function to retrieve the desired object, function or value

```typescript
import { $inject } from '@pyriter/anchorjs';

const foo = $inject<Foo>('foo');
console.log(foo.bar); // prints "foobar" to the console
```

### Using dependency injection to inject data providers

One useful thing about dependency injection is to configure your objects and then have them injected to other objects on
creation

```typescript
import { $install, DependencyType } from '@pyriter/anchorjs';

type Credential = {
  username: string,
  password: string
};

const credentials = {
  username: "username",
  password: "password"
};

type DataSourceProvider = {
  getCredentials: () => Credential
}

$install<DataSourceProvider>('dataStoreProvider', {
  provide: () => new DataSourceProvider(credentials),
  type: DependencyType.FACTORY
});
```

Then in a separate file, you can create another object that uses these providers without knowing how to set them up

```typescript
import { $inject } from '@pyriter/anchorjs';

class MyActionController {
  constructor(readonly private dataSourceProvider = $inject<DataSourceProvider>("dataSourceProvider")) {
  }

  public async getCredentials(): Promise<DataItem> {
    return this.dataSourceProvider.getCredentials();
  }
}
```

Now you can create the MyActionController object without having to know how to create the 2 dataSources

```typescript
const myActionController = new MyActionController();
```

# API

## $install

The $install function takes in 2 arguments: `key: string`, and `props: InjectProps`

### key

The key is a string value only (at this time)

### InjectProps

```typescript
type InjectProps = {
  provide: () => T,
  type: DependencyType
}
```

The type is used to determine if the provide function call be called for every $inject (Factory) or should the value be
retrieved by a lookup (Singleton). The default behavoir is `DependencyType.Factory`

## $inject

The $inject function is used to retrieve the desired object from the installation step. It takes in the return type and the key

```typescript
type $inject = (key: string)<T> => T;
```
