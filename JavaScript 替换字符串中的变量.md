```JavaScript
const isNull = (obj) => obj === null;
const isUndefined = (obj) => typeof obj === 'undefined';
const isNullOrUndefined = (obj) => isUndefined(obj) || isNull(obj);
const isObject = (obj) => !isNullOrUndefined(obj) && typeof obj === 'object' && !Array.isArray(obj)

function stringify(data) {
  switch (typeof data) {
    case 'undefined':
      return 'undefined'
    case 'boolean':
      return data ? 'true' : 'false'
    case 'number':
      return String(data)
    case 'string':
      return data
    case 'symbol':
      return data.toString()
    case 'function':
      return data.toString()
    case 'object':
      if (isNull(data)) {
        return 'null'
      }
      if (data instanceof Error) {
        return data.toString()
      }
      if (data instanceof Date) {
        return data.toISOString()
      }
      return JSON.stringify(data, null, 2)
    default:
      return 'unknown'
  }
}

function escapeRegex(str) {
  return str.replace(/[.*+?^${}()|[\]\\]/g, '\\$&');
}

function hasFunction(obj, method) {
  return (
    isObject(obj) &&
    method in obj &&
    typeof obj[method] === 'function'
  );
};
Date.prototype.format = function(fmt) {
  const padZero = (num) => num.toString().padStart(2, '0');

  const map = {
    'YYYY': this.getFullYear(),
    'MM': padZero(this.getMonth() + 1),
    'DD': padZero(this.getDate()),
    'HH': padZero(this.getHours()),
    'mm': padZero(this.getMinutes()),
    'ss': padZero(this.getSeconds())
  };

  return fmt.replace(/YYYY|MM|DD|HH|mm|ss/g, (key) => map[key]);
};
/**
 * Replace string variable
 * @param {Record<string, unknown>} replacements
 * @returns {string}
 * @example 
 * 'Hello AAAnameBBB'.replaceVariable({name: 'World'},'AAA','BBB') // 'Hello World'
 */
String.prototype.replaceVariable = function(replacements, prefix = '%#', suffix = '#%') {
  let current = this.toString();
  prefix = escapeRegex(prefix);
  suffix = escapeRegex(suffix);
  const seen = new Set();
  const patterns = Object.keys(replacements).map(key => {
    const escKey = escapeRegex(key);
    return {
      value: replacements[key],
      placeholderRegex: new RegExp(
        `${prefix}${escKey}(?=(?::.*?${suffix}|${suffix}))(?::.*?)?${suffix}`,
        "gs"
      ),
      placeholderFormatRegex: new RegExp(
        `(?<=${prefix}${escKey}(?=(?::.*?${suffix}|${suffix})):).*?(?=${suffix})`,
        "gs"
      )
    };
  });
  while (true) {
    if (seen.has(current)) break;
    seen.add(current);
    let next = current;
    for (const {
        value,
        placeholderRegex,
        placeholderFormatRegex
      } of patterns) {
      if (placeholderRegex.test(next)) {
        let format = next.match(placeholderFormatRegex)
        if (!isNullOrUndefined(format) && format.length != 0 && hasFunction(value, 'format')) {
          next = next.replace(placeholderRegex, stringify(value.format(format[0])));
        } else {
          next = next.replace(placeholderRegex, stringify(value instanceof Date ? value.format('YYYY-MM-DD') : value));
        }
      }
    }
    if (current === next) break;
    current = next;
  }
  return current;
};
var example = 'Hello ${name}! ${time}'.replaceVariable({
  name: 'World',
  time: new Date('2025-05-09T12:28:12')
}, '${', '}');
console.log(example);
```
