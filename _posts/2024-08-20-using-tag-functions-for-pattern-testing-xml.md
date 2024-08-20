---
layout: post
title: Using tag functions for pattern testing XML
author: Gabriel Galer
tags: [testing, template-literals, xml]
---

You are tasked with building a testing API that allows a nice and clean way to test XML patterns. The API should be able to test XML patterns in a way that is easy to read and understand.

For example, <span class="highlight-yellow">when testing an XML payload, you may be interested in the values or patterns of some nodes of the payload and not in the entire payload</span>. You may also want to test the presence of some nodes or attributes.

To do this, you can use a tag function that will parse the XML payload and return an object with the values of the nodes you are interested in. This way, you can test the values of the nodes in a more readable way.

## The tag function

First of all, let's talk about what a tag function is. A tag function is a function that is called with a template literal. The template literal is a string that contains placeholders that are replaced by the values passed to the tag function.

```typescript
// Update this type to match the values you expect
type ValuesType = RegExp | string | number | boolean | null | undefined;

function tagFunction(strings: TemplateStringsArray, ...values: ValuesType[]) {
  console.log('strings:', strings);
  console.log('values:', values);
}

tagFunction`Hello, ${'world'}!`;

// Output:
// strings: [ 'Hello, ', '!' ]
// values: [ 'world' ]

tagFunction`<payload>
<address>${/[a-zA-Z0-9]{1,100}/}</address>
<city>${'city'}</city>
<state>${'state'}</state>
</payload>`;

// Output:
// strings: [ '<payload>\n<address>', '</address>\n<city>', '</city>\n<state>', '</state>\n</payload>' ]
// values: [ /[a-zA-Z0-9]{1,100}/, 'city', 'state' ]
```

You can see how the tag function works in the example above. The `tagFunction` function is called with a template literal that contains placeholders. The `strings` parameter contains the strings of the template literal, and the `values` parameter contains the values passed to the tag function.

You can see more example in the MDN documentation: [Tagged Templates](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#tagged_templates).

## Testing XML patterns

Now that you know how tag functions work, you can use them to test XML patterns. You can create a tag function that will parse the XML payload and return an object with the values of the nodes you are interested in.

<span class="highlight-yellow">The catch is that whenever you want to test for a pattern in the XML, you will have to transform the entire XML payload in a pattern as well</span>. To do that, we will need to escape the `strings` for any special characters that could break the pattern, but keep the `values` as they are (if they are regular expressions):

```typescript
// test/utils/patterns.ts
type ValuesType = RegExp | string;

export function xml(strings: TemplateStringsArray, ...values: ValuesType[]) {
  return strings.reduce((acc, str, i) => {
    const minifiedStr = str.replace(/\s+/g, ' ').replace(/\n/g, '');
    const escapedStr = escapeRegExp(minifiedStr);

    if (values[i] instanceof RegExp) {
      return acc + escapedStr + values[i].source;
    } else if (typeof values[i] === 'string') {
      return acc + escapedStr + escapeRegExp(values[i]);
    }

    throw new Error('Invalid value type');
  }, '');
}
```

Now we can test the XML payload against a pattern that is composable and easy to read:

```typescript
// test/address_validator.ts
describe('XML payload', () => {
  it('should have the correct values', () => {
    const payload = `
      <payload>
        <address>123 Main St.</address>
        <city>Springfield</city>
        <state>IL</state>
      </payload>
    `;

    const addressPattern = /[a-zA-Z0-9]{1,100}/;
    const postalCodePattern = /[0-9]{5}/;
    const anneOrBobPattern = /Anne|Bob/;

    const expectedPattern = xml`
      <payload>
        <address>${addressPattern}</address>
        <postalCode>${postalCodePattern}</postalCode>
        <name>${anneOrBobPattern}</name>
      </payload>
    `;

    expect(minifyXml(payload)).toMatch(expectedPattern);
  });
});
```

And that's it! You now have a nice and clean way to test XML patterns using tag functions. You can easily test the values of the nodes in the XML payload in a way that is easy to read and understand.