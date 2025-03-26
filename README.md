#Installation
```
npm i @reduxjs/toolkit react-redux
```


# Redux Cart Implementation Documentation

This document outlines the implementation of a simple shopping cart feature using React and Redux Toolkit. It covers the React component, Redux store configuration, and the cart slice.

## File Structure
```
/src
 ├── app
 │    ├── store.js
 │    ├── cartSlice.js
 ├── components
 │    ├── Cart.js
 ├── assets
 │    ├── cart.svg
 ├── index.js
 ├── App.js

```

All code is contained within a single file for simplicity.

## React Component (Cart.js)



```javascript
import React, { useEffect, useState } from "react";
import { useSelector, useDispatch } from "react-redux";
import { addToCart, deleteItems } from "../app/cartSlice";

function Cart() {
  const dispatch = useDispatch();
  const cart = useSelector((state) => state.cart.cart);

  const addCart = (item) => {
    const exists = cart.some((cartItem) => cartItem.id === item.id);
    if (exists) {
      window.alert("Item already in cart!");
    } else {
      dispatch(addToCart(item));
    }
  };

  const removeItems = (id) => {
    dispatch(deleteItems(id));
  };

  const [product, setProduct] = useState([]);
  const [cartOpen, setCartOpen] = useState(false);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    setLoading(true);
    fetch("[https://fakestoreapi.com/products/](https://fakestoreapi.com/products/)")
      .then((res) => res.json())
      .then((data) => setProduct(data));
    setLoading(false);
  }, []);

  return (
    <div>
      <div className="flex items-center justify-around px-10 py-4">
        <div className="text-center font-bold text-2xl my-3">Products</div>
        <span className="cursor-pointer" onClick={() => setCartOpen(!cartOpen)}>
          <img src="cart.svg" alt="" />
        </span>
      </div>
      <div className="grid grid-cols-5 gap-7 mx-10">
        {loading ? (
          <span className="text-center col-span-full">Product Loading...</span>
        ) : (
          product.map((product) => {
            return (
              <div className="ring-1 bg-white ring-gray-400 p-5 rounded-md py-3">
                <img
                  src={product.image}
                  alt=""
                  className="w-[200px] h-[200px]"
                />
                <p className="text-xl">{product.title.slice(0, 13)}...</p>
                <div className="my-2 flex items-center w-full">
                  <button
                    onClick={() => addCart(product)}
                    className="rounded-md px-5 py-2 bg-red-500 cursor-pointer text-white hover:bg-red-600 duration-300"
                  >
                    Add to cart
                  </button>
                </div>
              </div>
            );
          })
        )}
      </div>
      <div
        className={`bg-white shadow-md overflow-y-scroll p-5 transform duration-200 ease-in-out ${
          cartOpen ? "" : "-translate-x-full"
        } w-1/3 h-screen fixed top-0`}
      >
        <h2 className="font-bold text-xl mb-3">Cart Items</h2>
        {cart.length === 0 ? (
          <p>No items added</p>
        ) : (
          cart.map((item, index) => (
            <div className="my-3" key={index}>
              <div className="flex items-center gap-2">
                <img src={item.image} className="w-12 h-12" alt="" />
                <p className="border-b py-2">{item.title}...</p>
                <span
                  onClick={() => removeItems(item.id)}
                  className="bg-red-500 text-white p-2 rounded-lg cursor-pointer"
                >
                  <p>X</p>
                </span>
              </div>
            </div>
          ))
        )}
      </div>
    </div>
  );
}

export default Cart;
```

# Redux Store Configuration (store.js)

This file configures the Redux store using Redux Toolkit's `configureStore` function.

```javascript

import { configureStore } from "@reduxjs/toolkit";
import cartSlice from "./cartSlice";

const store = configureStore({
  reducer: {
    cart: cartSlice,
  },
});

export default store;
```
# Redux Cart Slice (cartSlice.js)

This file defines the Redux slice for managing the cart state using Redux Toolkit's `createSlice` function.

```javascript
import { createSlice } from "@reduxjs/toolkit";

const cartSlice = createSlice({
  name: "cart",
  initialState: {
    cart: [],
  },
  reducers: {
    addToCart: (state, action) => {
      state.cart.push(action.payload);
    },
    deleteItems: (state, action) => {
      state.cart = state.cart.filter((item) => item.id !== action.payload);
    },
  },
});

export const { addToCart, deleteItems } = cartSlice.actions;
export default cartSlice.reducer;
