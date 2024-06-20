---
title: "Inmutable Types"
date: 2024-06-20
categories:
  - typescript
tags:
  - inmutable
  - typescript
---

Immutable types, as the name suggests, are types whose state cannot be modified after they are created. In simpler terms, once an immutable variable or object is assigned a value, that value cannot be changed. This contrasts with mutable types, where data can be altered freely throughout the program's execution.

In Typescript we have the [Readonly](https://www.typescriptlang.org/docs/handbook/utility-types.html#readonlytype) utility type for creating 'inmutable' versions of a predefined type. But it falls short for complex types:

    type  ReadOnlyCustomer  =  Readonly<{
	  name:  string;
	  email:  string;
	  shipAddress: {
	    address:  string;
	    zipcode:  string;
	  }
	}>;

	const  readonlyCustomer:  ReadOnlyCustomer  = {
	  name:  "Edu Ortega",
	  email:  "mail@domain.com",
	  shipAddress: {
	    address:  "48481 Dottie Street",
	    zipcode:  "14910-000",
	  },
	};

	//Error
	readonlyCustomer.name  =  "Eduardo";
	
	//Error
	readonlyCustomer.shipAddress  = {
	  address:  "48481 Dottie Street",
	  zipcode:  "14910-000",
	},
	
	//This is OK
	readonlyCustomer.shipAddress.address  =  "New one";

For types with nested attributes readonly prevents the developer from reassigning the entire object, not its properties. The solution for this is to create a new Type, I called it InmutableType and it uses the power generics to add the Readonly keyword to every property of the type regardless of the nesting level:

	type  InmutableType<T> = {
	  readonly [S  in  keyof  T]:  InmutableType<T[S]>;
	}

	type  InmutableCustomer  =  InmutableType<{
	  name:  string;
	  email:  string;
	  shipAddress: {
	    address:  string;
	    zipcode:  string;
	  }
	}>;

	const  inmutableCustomer:  InmutableCustomer  = {
	  name:  "Edu Ortega",
	  email:  "mail@domain.com",
	  shipAddress: {
	    address:  "48481 Dottie Street",
	    zipcode:  "14910-000",
	  },
	};

	//Error
	inmutableCustomer.name  =  "Eduardo";

	//Error
	inmutableCustomer.shipAddress  = {
	  address:  "48481 Dottie Street",
	  zipcode:  "14910-000"
	}

	//Error
	inmutableCustomer.shipAddress.address  =  "New one";


Until next time, Edu from the past.

Happy coding!
