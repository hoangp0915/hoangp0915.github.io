# ReadMoney
```javascript
function readGroup(group) {
  const readDigit = [
    ' không',
    ' một',
    ' hai',
    ' ba',
    ' bốn',
    ' năm',
    ' sáu',
    ' bảy',
    ' tám',
    ' chín',
  ];
  let result = '';
  let hundreds = parseInt(group / 100);
  let dozens = parseInt((group % 100) / 10);
  let units = group % 10;

  if (hundreds != 0) {
    result += readDigit[hundreds] + ' trăm';
    if (dozens == 0 && units != 0) result += ' linh';
  }
  if (dozens != 0 && dozens != 1) {
    result += readDigit[dozens] + ' mươi';
  }

  if (dozens == 1) result += ' mười';

  switch (units) {
    case 1:
      if (dozens != 0 && dozens != 1) {
        result += ' mốt ';
      } else {
        result += readDigit[units];
      }
      break;
    case 5:
      if (dozens == 0) {
        result += readDigit[units];
      } else {
        result += ' lăm';
      }
      break;
    default:
      if (units != 0) {
        result += readDigit[units];
      }
      break;
  }
  return result;
}

function readMoney(num, unit) {
  if (num < 0) {
    return `Số ${unit} âm!`;
  }
  if (num === 0) {
    return `Không ${unit}`;
  }
  if (num > 8999999999999999) {
    return 'Số quá lớn, không thể tính nổi!';
  }

  const currencyArr = [
    { func: (s) => parseInt(s % 1000), value: '', key: 1 },
    { func: (s) => parseInt((s % 1000000) / 1000), value: ' nghìn', key: 1000 },
    { func: (s) => parseInt(s / 1000000), value: ' triệu', key: 1000000 },
    { func: (s) => Math.floor(s / 1000000000), value: ' tỷ', key: 1000000000 },
    {
      func: (s) => Math.floor(s / 1000000000000),
      value: 'nghìn tỷ',
      key: 1000000000000,
    },
    {
      func: (s) => Math.floor(s / 1000000000000000),
      value: 'triệu tỷ',
      key: 1000000000000000,
    },
  ];
  let index = 0;
  const positions = [];

  for (let i = currencyArr.length - 1; i >= 0; i--) {
    const calc = currencyArr[i].func(num);
    positions[i] = calc;
    if (calc > 0 && index == 0) {
      index = i;
    }
    if (currencyArr[i].key >= 1000000000) {
      num = num - parseFloat(positions[i].toString()) * currencyArr[i].key;
    }
  }

  let result = '';
  for (let j = index; j >= 0; j--) {
	const tmp = readGroup(positions[j])
	result += tmp;
	if (positions[j] > 0) {
		result += currencyArr[j].value;
	}
  }
  return `${result} ${unit}`.trim()
}

var cv1 = readMoney(-1, "mét vuông");
console.log(`[🚀] ~ cv1`, cv1);
var cv2 = readMoney(10202, "ki lô mét");
console.log(`[🚀] ~ cv2`, cv2);
var cv3 = readMoney(25000, "vnd");
console.log(`[🚀] ~ cv3`, cv3);
var cv4 = readMoney(100010000100, "vnd");
console.log(`[🚀] ~ cv4`, cv4);

```
