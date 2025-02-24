# CRUD Data Stores

The CRUD data store is a set of utilities to allow faster and less error prone creation of data stores that have create, read, update, and delete capabilities.

## Usage

The CRUD data store methods can be used in one of a couple ways.

### Default data store

If the default CRUD actions work well for your use case, you can use the more opinionated setup.

```js
import { createCrudDataStore } from '../crud';

createCrudDataStore( {
	storeName: 'my/custom/store',
	resourceName: 'MyThing',
	pluralResourceName: 'MyThings',
	namespace: '/my/rest/namespace',
} );
```

This will register a data store named `my/custom/store` with the following default selectors:

| Selector | Description |
| --- | --- |
| `getMyThing( id )` | Get an item by ID |
| `getMyThingError( id )` | Get the error for an item. |
| `getMyThings( query = {} )` | Get all items, optionally by a specific query. |
| `getMyThingsError( query = {} )` | Get the error for a set of items by query. |

The following resolvers will also be added:

| Resolver | Method | Endpoint |
| --- | --- | --- |
| `getMyThing( id )` | GET | `<namespace>/<id>` |
| `getMyThings( query = {} )` | GET | `<namespace>` |

### Customized data store

If the default settings are not adequate for your needs, you can always create your own data store and supplement the default CRUD actions with your own.

```js
import { createSelectors } from '../crud/selectors';
import { createResolvers } from '../crud/selectors';
import * as crudActions from '../crud/actions';
import { registerStore, combineReducers } from '@wordpress/data';

const dataStoreArgs = {
    resourceName: 'MyThing',
    pluralResourceName: 'MyThings',
}

const crudSelectors = createSelectors( dataStoreArgs )
const crudResolvers = createResolvers( { ...dataStoreArgs, namespace: 'my/rest/namespace' } )

registerStore( 'my/custom/store', {
	reducer: combineReducers( { reducer, myReducer } ),
	actions: { ...crudActions, myActions },
	controls,
	selectors: { ...crudSelectors, mySelectors },
	resolvers: { ...crudResolvers, myResolvers },
} );
```

## Structure

The data store schema is set in such a way that allows queries to be cached and previously downloaded resources to be more readily available.

```js
{
    items: {
        21: { ... },
    },
    errors: {
        'GET_ITEMS:page=3': 'There was an error trying to fetch page 3',
    },
    data: {
        'GET_ITEMS:page=2' : [ 21 ],
    }
}
```

By default, the CRUD data store expects a property of `id` to be present on all resources.