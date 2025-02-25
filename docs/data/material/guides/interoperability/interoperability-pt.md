# Style library interoperability

<p class="description">While you can use the Emotion-based styling solution provided by MUI to style your application, you can also use the one you already know and love (from plain CSS to styled-components).</p>

Este guia tem como objetivo documentar as alternativas mais populares, mas você deve descobrir que os princípios aplicados aqui podem ser adaptados para outras bibliotecas. Existem exemplos para as seguintes soluções de estilo:

- [CSS puro](#plain-css)
- [CSS global](#global-css)
- [Styled Components](#styled-components)
- [Módulos CSS](#css-modules)
- [Emotion](#emotion)
- [Tailwind CSS](#tailwind-css)
- [~~JSS~~ TSS](#jss-tss)

## CSS puro

Nada extravagante, apenas CSS.

{{"demo": "StyledComponents.js", "hideToolbar": true}}

[![Botão editar](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/plain-css-fdue7)

**PlainCssSlider.css**

```css
.slider {
  color: #20b2aa;
}

.slider:hover {
  color: #2e8b57;
}
```

**PlainCssSlider.js**

```jsx
import * as React from 'react';
import Slider from '@material-ui/core/Slider';
import './PlainCssSlider.css';

export default function PlainCssSlider() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider defaultValue={30} className="slider" />
    </div>
  );
}
```

### Ordem de injeção do CSS ⚠️

**Nota:** A maioria das soluções CSS-in-JS injetam seus estilos na parte inferior do HTML `<head>`, que dá precedência ao Material-UI sobre seus estilos customizados. Para remover a necessidade de **!important**, você precisa alterar a ordem de injeção do CSS. Here's a demo of how it can be done in Material-UI:

```jsx
import * as React from 'react';
import { StyledEngineProvider } from '@material-ui/core/styles';

export default function GlobalCssPriority() {
  return (
    <StyledEngineProvider injectFirst>
      {/* Your component tree. Now you can override MUI's styles. */}
    </CacheProvider>
  );
}
```

**Note:** If you are using Emotion and have a custom cache in your app, that one will override the one coming from MUI. In order for the injection order to still be correct, you need to add the prepend option. Aqui está um exemplo:

```jsx
import * as React from 'react';
import { StyledEngineProvider } from '@material-ui/core/styles';

export default function GlobalCssPriority() {
  return (
    <StyledEngineProvider injectFirst>
      {/* Your component tree. Now you can override MUI's styles. */}
    </CacheProvider>
  );
}
```

**Note:** If you are using styled-components and have `StyleSheetManager` with a custom `target`, make sure that the target is the first element in the HTML `<head>`. If you are curious to see how it can be done, you can take a look on the `StylesProvider` implementation in the `@material-ui/styled-engine-sc` package.

### Elementos mais profundos

Se você tentar estilizar o Slider, você provavelmente gostaria de afetar alguns dos elementos filhos de Slider, por exemplo o thumb. No Material-UI, todos os elementos filhos têm uma especificidade aumentada de 2: `.parent .child {}`. Ao escrever uma sobrescrita, você precisa fazer o mesmo.

Os exemplos a seguir substituem o estilo de `thumb` do controle slider, além dos estilos customizados no slider em si.

{{"demo": "StyledComponentsDeep.js", "hideToolbar": true}}

**PlainCssSliderDeep1.css**

```css
.slider {
  color: #20b2aa;
}

.slider:hover {
  color: #2e8b57;
}

.slider . MuiSlider-thumb {
  border-radius: 1px;
}
```

**PlainCssSliderDeep1.js**

```jsx
import * as React from 'react';
import Slider from '@material-ui/core/Slider';
import './PlainCssSliderDeep1.css';

export default function PlainCssSliderDeep1() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider defaultValue={30} className="slider" />
    </div>
  );
}
```

The above demo relies on the [default `className` values](/system/styles/advanced/), but you can provide your own class name with the `componentsProps` API.

**PlainCssSliderDeep2.css**

```css
.slider {
  color: #20b2aa;
}

.slider:hover {
  color: #2e8b57;
}

.slider .thumb {
  border-radius: 1px;
}
```

**PlainCssSliderDeep2.js**

```jsx
import * as React from 'react';
import Slider from '@material-ui/core/Slider';
import './PlainCssSliderDeep2.css';

export default function PlainCssSliderDeep2() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider
        defaultValue={30}
        className="slider"
        componentsProps={{ thumb: { className: 'thumb' } }}
      />
    </div>
  );
}
```

## CSS global

Fornecer explicitamente os nomes das classes ao componente é um esforço excessivo? [You can target the class names generated by MUI](/system/styles/advanced/).

[![Botão editar](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/global-classnames-dho8k)

**GlobalCssSlider.css**

```css
. MuiSlider-root {
  color: #20b2aa;
}

. MuiSlider-root:hover {
  color: #2e8b57;
}
```

**GlobalCssSlider.js**

```jsx
import * as React from 'react';
import Slider from '@material-ui/core/Slider';
import './GlobalCssSlider.css';

export default function GlobalCssSlider() {
  return <Slider defaultValue={30} />;
}
```

### Ordem de injeção do CSS ⚠️

Para remover a necessidade de **!important**, você precisa alterar a ordem de injeção do CSS. **Nota:** A maioria das soluções CSS-in-JS injetam seus estilos na parte inferior do HTML `<head>`, que dá precedência ao Material-UI sobre seus estilos customizados. Here's a demo of how it can be done in Material-UI:

```jsx
import * as React from 'react';
import { StyledEngineProvider } from '@material-ui/core/styles';

export default function GlobalCssPriority() {
  return (
    <StyledEngineProvider injectFirst>
      {/* Your component tree. Now you can override MUI's styles. import * as React from 'react';
import { CacheProvider } from '@emotion/react';
import createCache from '@emotion/cache';

const cache = createCache({
  key: 'css',
  prepend: true,
});

export default function CssModulesPriority() {
  return (
    <CacheProvider value={cache}>
      {/* Sua árvore de componentes.
```

**Note:** If you are using Emotion and have a custom cache in your app, that one will override the one coming from MUI. In order for the injection order to still be correct, you need to add the prepend option. Aqui está um exemplo:

```jsx
Agora você pode sobrescrever os estilos do Material-UI. import * as React from 'react';
import { StylesProvider } from '@material-ui/core';

export default function GlobalCssPriority() {
  return (
    <StylesProvider injectFirst>
      {/* Your component tree. import * as React from 'react';
import { CacheProvider } from '@emotion/react';
import createCache from '@emotion/cache';

const cache = createCache({
  key: 'css',
  prepend: true,
});

export default function CssModulesPriority() {
  return (
    <CacheProvider value={cache}>
      {/* Sua árvore de componentes. */}
    </CacheProvider>
  );
}
```

**Note:** If you are using styled-components and have `StyleSheetManager` with a custom `target`, make sure that the target is the first element in the HTML `<head>`. If you are curious to see how it can be done, you can take a look on the `StylesProvider` implementation in the `@material-ui/styled-engine-sc` package.

### Elementos mais profundos

Se você tentar estilizar o Slider, você provavelmente gostaria de afetar alguns dos elementos filhos de Slider, por exemplo o thumb. No Material-UI, todos os elementos filhos têm uma especificidade aumentada de 2: `.parent .child {}`. Ao escrever uma sobrescrita, você precisa fazer o mesmo.

O exemplo a seguir substituem o estilo de `thumb` do controle slider, além dos estilos customizados no slider em si.

{{"demo": "StyledComponentsDeep.js", "hideToolbar": true}}

**GlobalCssSliderDeep.css**

```css
. MuiSlider-root {
  color: #20b2aa;
}

. MuiSlider-root:hover {
  color: #2e8b57;
}

. MuiSlider-root . MuiSlider-thumb {
  border-radius: 1px;
}
```

**GlobalCssSliderDeep.js**

```jsx
import * as React from 'react';
import Slider from '@material-ui/core/Slider';
import './GlobalCssSliderDeep.css';

export default function GlobalCssSliderDeep() {
  return <Slider defaultValue={30} />;
}
```

## Styled Components

![estrelas](https://img.shields.io/github/stars/styled-components/styled-components.svg?style=social&label=Star) ![npm](https://img.shields.io/npm/dm/styled-components.svg)

### Alterar o motor de estilo padrão

By default, MUI components come with Emotion as their style engine. If, however, you would like to use `styled-components`, you can configure your app by following the [styled engine guide](/material-ui/guides/styled-engine/#how-to-switch-to-styled-components) or starting with one of the example projects:

<!-- #default-branch-switch -->

- [Create React App with styled-components](https://github.com/mui/material-ui/tree/master/examples/material-cra-styled-components)
- [Create React App with styled-components and typescript](https://github.com/mui/material-ui/tree/master/examples/material-cra-styled-components-ts)

After the style engine is configured properly, you can use the [`styled()`](/system/styled/) utility from `@material-ui/core/styles` and have direct access to the theme.

After the style engine is configured properly, you can use the [`styled()`](/system/styled/) utility from `@mui/material/styles` and have direct access to the theme.

{{"demo": "StyledComponents.js", "hideToolbar": true}}

[![Botão editar](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/styled-components-interoperability-w9z9d)

```jsx
import * as React from 'react';
import Slider from '@material-ui/core/Slider';
import { styled } from '@material-ui/core/styles';

const CustomizedSlider = styled(Slider)`
  color: #20b2aa;

  :hover {
    color: #2e8b57;
  }
`;

export default function StyledComponents() {
  return <CustomizedSlider defaultValue={30} />;
}
```

### Elementos mais profundos

Se você tentar estilizar o Slider, você provavelmente gostaria de afetar alguns dos elementos filhos de Slider, por exemplo o thumb. No Material-UI, todos os elementos filhos têm uma especificidade aumentada de 2: `.parent .child {}`. Ao escrever uma sobrescrita, você precisa fazer o mesmo.

Os exemplos a seguir substituem o estilo de `thumb` do controle slider, além dos estilos customizados no slider em si.

{{"demo": "StyledComponentsDeep.js", "defaultCodeOpen": true}}

The above demo relies on the [default `className` values](/system/styles/advanced/), but you can provide your own class name with the `componentsProps` API.

```jsx
import * as React from 'react';
import { styled } from '@material-ui/core/styles';
import Slider from '@material-ui/core/Slider';

const CustomizedSlider = styled((props) => (
  <Slider componentsProps={{ thumb: { className: 'thumb' } }} {...props} />
))`
  color: #20b2aa;

  :hover {
    color: #2e8b57;
  }

  & .thumb {
    border-radius: 1px;
  }
`;

export default function StyledComponentsDeep2() {
  return (
    <div>
      <Slider defaultValue={30} />
      <CustomizedSlider defaultValue={30} />
    </div>
  );
}
```

### Tema

Você é encorajado a compartilhar o mesmo objeto de tema entre Material-UI e o resto de seu projeto.

:::warning
If you are **already** using a custom theme with styled-components or emotion, it might not be compatible with MUI's theme specification. If it's not compatible, you need to render MUI's ThemeProvider <b>first</b>. This will ensure the theme structures are isolated. This is ideal for the progressive adoption of MUI's components in the codebase.
:::

You are encouraged to share the same theme object between MUI and the rest of your project.

```jsx
const CustomizedSlider = styled(Slider)(
  ({ theme }) => `
  color: ${theme.palette.primary.main};

  :hover {
    color: ${darken(theme.palette.primary.main, 0.2)};
  }
`,
);
```

{{"demo": "StyledComponentsTheme.js"}}

### Portais

The [Portal](/material-ui/react-portal/) provides a first-class way to render children into a DOM node that exists outside the DOM hierarchy of the parent component. Because of the way styled-components scopes its CSS, you may run into issues where styling is not applied.

For example, if you attempt to style the `tooltip` generated by the [Tooltip](/material-ui/react-tooltip/) component, you will need to pass along the `className` property to the element being rendered outside of it's DOM hierarchy. The following example shows a workaround:

```jsx
import * as React from 'react';
import { styled } from '@material-ui/core/styles';
import Button from '@material-ui/core/Button';
import Tooltip from '@material-ui/core/Tooltip';

const StyledTooltip = styled(({ className, ...props }) => (
  <Tooltip {...props} classes={{ popper: className }} />
))`
  & . MuiTooltip-tooltip {
    background: navy;
  }
`;
```

{{"demo": "StyledComponentsPortal.js"}}

## Módulos CSS

![stars](https://img.shields.io/github/stars/css-modules/css-modules.svg?style=social&label=Star)

It's hard to know the market share of [this styling solution](https://github.com/css-modules/css-modules) as it's dependent on the bundling solution people are using.

{{"demo": "StyledComponents.js", "hideToolbar": true}}

[![Edit Button](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/css-modules-nuyg8)

**CssModulesSlider.module.css**

```css
.slider {
  color: #20b2aa;
}

.slider:hover {
  color: #2e8b57;
}
```

**CssModulesSlider.js**

```jsx
import * as React from 'react';
import Slider from '@material-ui/core/Slider';
// webpack, parcel or else will inject the CSS into the page
import styles from './CssModulesSlider.module.css';

export default function CssModulesSlider() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider defaultValue={30} className={styles.slider} />
    </div>
  );
}
```

### Ordem de injeção do CSS ⚠️

**Note:** Most CSS-in-JS solutions inject their styles at the bottom of the HTML `<head>`, which gives MUI precedence over your custom styles. To remove the need for **!important**, you need to change the CSS injection order. Here's a demo of how it can be done in MUI:

```jsx
import * as React from 'react';
import { StyledEngineProvider } from '@material-ui/core/styles';

export default function GlobalCssPriority() {
  return (
    <StyledEngineProvider injectFirst>
      {/* Your component tree. Now you can override MUI's styles. */}
    </StyledEngineProvider>
  );
}
```

**Note:** If you are using Emotion and have a custom cache in your app, that one will override the one coming from MUI. In order for the injection order to still be correct, you need to add the prepend option. Here is an example:

```jsx
*/}
    </StylesProvider>
  );
} import * as React from 'react';
import { CacheProvider } from '@emotion/react';
import createCache from '@emotion/cache';

const cache = createCache({
  key: 'css',
  prepend: true,
});

export default function CssModulesPriority() {
  return (
    <CacheProvider value={cache}>
      {/* Sua árvore de componentes. Now you can override MUI's styles. */}
    </CacheProvider>
  );
}
```

**Note:** If you are using styled-components and have `StyleSheetManager` with a custom `target`, make sure that the target is the first element in the HTML `<head>`. If you are curious to see how it can be done, you can take a look on the [`StyledEngineProvider`](https://github.com/mui/material-ui/blob/master/packages/mui-styled-engine-sc/src/StyledEngineProvider/StyledEngineProvider.js) implementation in the `@mui/styled-engine-sc` package.

### Elementos mais profundos

If you attempt to style the Slider, you will likely need to affect some of the Slider's child elements, for example the thumb. In MUI, all child elements have an increased specificity of 2: `.parent .child {}`. When writing overrides, you need to do the same. It's important to keep in mind that CSS Modules adds an unique id to each class, and that id won't be present on the MUI provided children class. To escape from that, CSS Modules provides a functionality, the `:global` selector.

The following examples override the slider's `thumb` style in addition to the custom styles on the slider itself.

{{"demo": "StyledComponentsDeep.js", "hideToolbar": true}}

**CssModulesSliderDeep1.module.css**

```css
.slider {
  color: #20b2aa;
}

.slider:hover {
  color: #2e8b57;
}

.slider :global .MuiSlider-thumb {
  border-radius: 1px;
}
```

**CssModulesSliderDeep1.js**

```jsx
import * as React from 'react';
// webpack, parcel or else will inject the CSS into the page
import styles from './CssModulesSliderDeep1.module.css';
import Slider from '@material-ui/core/Slider';

export default function CssModulesSliderDeep1() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider defaultValue={30} className={styles.slider} />
    </div>
  );
}
```

The above demo relies on the [default `className` values](/system/styles/advanced/), but you can provide your own class name with the `componentsProps` API.

**CssModulesSliderDeep2.module.css**

```css
.slider {
  color: #20b2aa;
}

.slider:hover {
  color: #2e8b57;
}

.slider .thumb {
  border-radius: 1px;
}
```

**CssModulesSliderDeep2.js**

```jsx
import * as React from 'react';
// webpack, parcel or else will inject the CSS into the page
import styles from './CssModulesSliderDeep2.module.css';
import Slider from '@material-ui/core/Slider';

export default function CssModulesSliderDeep2() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider
        defaultValue={30}
        className={styles.slider}
        componentsProps={{ thumb: { className: styles.thumb } }}
      />
    </div>
  );
}
```

## Emotion

![stars](https://img.shields.io/github/stars/emotion-js/emotion.svg?style=social&label=Star) ![npm](https://img.shields.io/npm/dm/@emotion/react.svg)

### A propriedade `css`

Emotion's **css()** method works seamlessly with MUI.

{{"demo": "EmotionCSS.js", "defaultCodeOpen": true}}

### Tema

Funciona exatamente como styled components. You can [use the same guide](/material-ui/guides/interoperability/#styled-components).

### A API `styled()`

It works exactly like styled components. You can [use the same guide](/material-ui/guides/interoperability/#styled-components).

## Tailwind CSS

![stars](https://img.shields.io/github/stars/tailwindlabs/tailwindcss.svg?style=social&label=Star) ![npm](https://img.shields.io/npm/dm/tailwindcss)

### Setup

If you are used to Tailwind CSS and want to use it together with the MUI components, you can start by cloning the [Tailwind CSS](https://github.com/mui/material-ui/tree/master/examples/material-cra-tailwind-ts) example project. If you use a different framework, or already have set up your project, follow these steps:

1. Add Tailwind CSS to your project, following the instructions in https://tailwindcss.com/docs/installation.
2. Remove [Tailwind CSS's preflight](https://tailwindcss.com/docs/preflight) style so it can use the MUI's preflight instead ([CssBaseline](/material-ui/react-css-baseline/)).

**tailwind.config.js**

```diff
 module.exports = {
+  corePlugins: {
+    preflight: false,
+  },
 };
```

3. Add the `important` option, using the id of your app wrapper. For example, `#__next` for Next.js and `"#root"` for CRA:

**tailwind.config.js**

```diff
 module.exports = {
   content: [
     "./src/**/*.{js,jsx,ts,tsx}",
   ],
+  important: '#root',
   theme: {
     extend: {},
   },
   plugins: [],
 }

```

Most of the CSS used by Material UI has as specificity of 1, hence this `important` property is unnecessary. However, in a few edge cases, MUI uses nested CSS selectors that win over Tailwind CSS. Use this step to help ensure that the [deeper elements](#deeper-elements-5) can always be customized using Tailwind's utility classes. More details on this option can be found here https://tailwindcss.com/docs/configuration#selector-strategy

4. Fix the CSS injection order. Most CSS-in-JS solutions inject their styles at the bottom of the HTML `<head>`, which gives MUI precedence over Tailwind CSS. To reduce the need for the `important` property, you need to change the CSS injection order. Here's a demo of how it can be done in MUI:

```jsx
import * as React from 'react';
import { StyledEngineProvider } from '@mui/material/styles';

export default function GlobalCssPriority() {
  return (
    <StyledEngineProvider injectFirst>
      {/* Your component tree. Now you can override MUI's styles. */}
    </StyledEngineProvider>
  );
}
```

**Note:** If you are using Emotion and have a custom cache in your app, it will override the one coming from MUI. In order for the injection order to still be correct, you need to add the prepend option. Here is an example:

```jsx
*/}
    </StylesProvider>
  );
} import * as React from 'react';
import { CacheProvider } from '@emotion/react';
import createCache from '@emotion/cache';

const cache = createCache({
  key: 'css',
  prepend: true,
});

export default function CssModulesPriority() {
  return (
    <CacheProvider value={cache}>
      {/* Sua árvore de componentes. Now you can override MUI's styles. */}
    </CacheProvider>
  );
}
```

**Note:** If you are using styled-components and have `StyleSheetManager` with a custom `target`, make sure that the target is the first element in the HTML `<head>`. If you are curious to see how it can be done, you can take a look at the [`StyledEngineProvider`](https://github.com/mui/material-ui/blob/master/packages/mui-styled-engine-sc/src/StyledEngineProvider/StyledEngineProvider.js) implementation in the `@mui/styled-engine-sc` package.

5. Change the target container for `Portal`-related elements so that they are injected under the main app wrapper that was used in step 3 for setting up the `important` option in the Tailwind config.

```jsx
const rootElement = document.getElementById("root");
const root = createRoot(rootElement);

const theme = createTheme({
  components: {
    MuiPopover: {
      defaultProps: {
        container: rootElement,
      },
    },
    MuiPopper: {
      defaultProps: {
        container: rootElement,
      },
    },
  },
});

root.render(
  <StyledEngineProvider injectFirst>
    <ThemeProvider theme={theme}>
      <App />
    </ThemeProvider>
  </StyledEngineProvider>;
);
```

### Usage

Now it's all set up and you can start using Tailwind CSS on the MUI components!

{{"demo": "StyledComponents.js", "hideToolbar": true}}

[![Edit on StackBlitz](https://developer.stackblitz.com/img/open_in_stackblitz.svg)](https://stackblitz.com/edit/github-ndkshy?file=pages%2Findex.tsx)

**index.tsx**

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';

export default function App() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider defaultValue={30} className="text-teal-600" />
    </div>
  );
}
```

### Deeper elements

If you attempt to style the Slider, for example, you'll likely want to customize its child elements.

This example showcases how to override the Slider's `thumb` style.

{{"demo": "StyledComponentsDeep.js", "hideToolbar": true}}

**SliderThumbOverrides.tsx**

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';

export default function SliderThumbOverrides() {
  return (
    <div>
      <Slider defaultValue={30} />
      <Slider
        defaultValue={30}
        className="text-teal-600"
        componentsProps={{ thumb: { className: 'rounded-sm' } }}
      />
    </div>
  );
}
```

### Styling pseudo states

If you want to style a component's pseudo-state, you can use the appropriate key in the `classes` prop. Here is an example of how you can style the Slider's active state:

**SliderPseudoStateOverrides.tsx**

```jsx
import * as React from 'react';
import Slider from '@mui/material/Slider';

export default function SliderThumbOverrides() {
  return <Slider defaultValue={30} classes={{ active: 'shadow-none' }} />;
}
```

## ~~JSS~~ TSS

[JSS](https://cssinjs.org/) itself is no longer supported in MUI however, if you like the hook-based API (`makeStyles` → `useStyles`) that [`react-jss`](https://codesandbox.io/s/j3l06yyqpw) was offering you can opt for [`tss-react`](https://github.com/garronej/tss-react).

[TSS](https://docs.tss-react.dev) integrates well with MUI and provide a better TypeScript support than JSS.

:::info
If you are updating from `@material-ui/core` (v4) to `@mui/material` (v5), check out the [tss-react section](/material-ui/migration/migrating-from-jss/#2-use-tss-react) of the Migration guide.
:::

```tsx
import { render } from 'react-dom';
import { CacheProvider } from '@emotion/react';
import createCache from '@emotion/cache';
import { ThemeProvider } from '@mui/material/styles';

export const muiCache = createCache({
  key: 'mui',
  prepend: true,
});

//NOTE: Don't use <StyledEngineProvider injectFirst/>
render(
  <CacheProvider value={muiCache}>
    <ThemeProvider theme={myTheme}>
      <Root />
    </ThemeProvider>
  </CacheProvider>,
  document.getElementById('root'),
);
```

Now you can simply `import { makeStyles, withStyles } from 'tss-react/mui'`. The theme object that will be passed to your callbacks functions will be the one you get with `import { useTheme } from '@mui/material/styles'`.

If you want to take controls over what the `theme` object should be, you can re-export `makeStyles` and `withStyles` from a file called, for example, `makesStyles.ts`:

```ts
import { useTheme } from '@mui/material/styles';
//WARNING: tss-react require TypeScript v4.4 or newer. If you can't update use:
//import { createMakeAndWithStyles } from "tss-react/compat";
import { createMakeAndWithStyles } from 'tss-react';

export const { makeStyles, withStyles } = createMakeAndWithStyles({
  useTheme,
  /*
    OR, if you have extended the default mui theme adding your own custom properties:
    Let's assume the myTheme object that you provide to the <ThemeProvider /> is of
    type MyTheme then you'll write:
    */
  //"useTheme": useTheme as (()=> MyTheme)
});
```

Then, the library is used like this:

```tsx
import { makeStyles } from 'tss-react/mui';

export function MyComponent(props: Props) {
  const { className } = props;

  const [color, setColor] = useState<'red' | 'blue'>('red');

  const { classes, cx } = useStyles({ color });

  //Thanks to cx, className will take priority over classes.root
  return <span className={cx(classes.root, className)}>hello world</span>;
}

const useStyles = makeStyles<{ color: 'red' | 'blue' }>()((theme, { color }) => ({
  root: {
    color,
    '&:hover': {
      backgroundColor: theme.palette.primary.main,
    },
  },
}));
```

For info on how to setup SSR or anything else, please refer to [the TSS documentation](https://github.com/garronej/tss-react).

:::info
There is [an ESLint plugin](https://docs.tss-react.dev/detecting-unused-classes) for detecting unused classes.
:::

:::warning
**Keep `@emotion/styled` as a dependency of your project**. Even if you never use it explicitly, it's a peer dependency of `@mui/material`.
:::

:::warning
For [Storybook](https://storybook.js.org): At the time of this writing, Storybook still uses Emotion 10 by default.
Material UI and TSS use Emotion 11, so you must make [some changes](https://github.com/InseeFrLab/onyxia-ui/blob/324de62248074582b227e584c53fb2e123f5325f/.storybook/main.js#L31-L32) to `.storybook/main.js` in order to use Storybook with Material UI.
:::
