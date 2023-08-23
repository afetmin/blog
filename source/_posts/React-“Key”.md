---
title: React “Key”
date: 2023-08-24 06:51:02
categories: React
---

### 什么是key属性以及为什么React需要它？

如果存在“key”属性，React 使用它作为在重新渲染期间在其兄弟姐妹中识别相同类型元素的一种方式，也就是说，仅在重新渲染期间和相同类型的相邻元素才需要它。

```jsx
const Item = ({ country }) => {
  return (
    <button className="country-item">
      <img src={country.flagUrl} />
      {country.name}
    </button>
  );
};

const CountriesList = ({ countries }) => {
  return (
    <div>
      {countries.map((country) => (
        <Item country={country} />
      ))}
    </div>
  );
};

// same as 
countries.map((country, index) => <Item country={country} key={index} />);
```

React 会发现那里没有“键”，然后回退到使用countries数组的索引作为键

### 什么情况下可以使用 index 作为 key呢？

**分页列表**<br />如果你希望在相同大小的列表中显示相同类型的不同项目。如果您采用key="id"方法，那么每次更改页面时，都会加载具有完全不同 ID 的全新项目集。这意味着 React 将无法找到任何“现有”项目，卸载整个列表，并安装全新的项目集。但！如果你采用key="index"这种方法，React 会认为新“页面”上的所有项目都已经存在，并且只会用新数据更新这些项目，而实际组件会被挂载。