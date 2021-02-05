```javascript
var str = `{
    1: "2"
}`;

function repeat(string, count) {
  var result = '', cycle;

  for (cycle = 0; cycle < count; cycle += 1) {
    result += string;
  }

  return result;
}

function padStart(string, max) {
  return repeat(' ', max - string.length) + string;
}

function getLine(string, lineStart, lineEnd, position, maxLineLength) {
    var head = '';
    var tail = '';
    var maxHalfLength = Math.floor(maxLineLength / 2) - 1;
  
    if (position - lineStart > maxHalfLength) {
      head = ' ... ';
      lineStart = position - maxHalfLength + head.length;
    }
  
    if (lineEnd - position > maxHalfLength) {
      tail = ' ...';
      lineEnd = position + maxHalfLength - tail.length;
    }
  
    return {
      str: head + string.slice(lineStart, lineEnd).replace(/\t/g, '→') + tail,
      pos: position - lineStart + head.length // relative position
    };
  }

function makeSnippet(mark, options) {
    options = Object.create(options || null);
  
    if (!mark.string) return null;
  
    if (!options.maxLength) options.maxLength = 79;
    if (typeof options.indent      !== 'number') options.indent      = 1;
    if (typeof options.linesBefore !== 'number') options.linesBefore = 3;
    if (typeof options.linesAfter  !== 'number') options.linesAfter  = 2;
  
    var re = /\r?\n|\r|\0/g;
    var lineStarts = [ 0 ];
    var lineEnds = [];
    var match;
    var foundLineNo = -1;
  
    while ((match = re.exec(mark.string))) {
      lineEnds.push(match.index);
      lineStarts.push(match.index + match[0].length);
  
      if (mark.position <= match.index && foundLineNo < 0) {
        foundLineNo = lineStarts.length - 2;
      }
    }
  
    if (foundLineNo < 0) foundLineNo = lineStarts.length - 1;
  
    var result = '', i, line;
    var lineNoLength = Math.min(mark.line + options.linesAfter, lineEnds.length).toString().length;
    var maxLineLength = options.maxLength - (options.indent + lineNoLength + 3);
  
    for (i = 1; i <= options.linesBefore; i++) {
      if (foundLineNo - i < 0) break;
      line = getLine(
        mark.string,
        lineStarts[foundLineNo - i],
        lineEnds[foundLineNo - i],
        mark.position - (lineStarts[foundLineNo] - lineStarts[foundLineNo - i]),
        maxLineLength
      );
      result = repeat(' ', options.indent) + padStart((mark.line - i + 1).toString(), lineNoLength) +
        ' | ' + line.str + '\n' + result;
    }
  
    line = getLine(mark.string, lineStarts[foundLineNo], lineEnds[foundLineNo], mark.position, maxLineLength);
    result += repeat('> ', options.indent) + padStart((mark.line + 1).toString(), lineNoLength) +
      ' | ' + line.str + '\n';
    result += repeat(' ', options.indent + lineNoLength + 3 + line.pos) + '^' + '\n';
  
    for (i = 1; i <= options.linesAfter; i++) {
      if (foundLineNo + i >= lineEnds.length) break;
      line = getLine(
        mark.string,
        lineStarts[foundLineNo + i],
        lineEnds[foundLineNo + i],
        mark.position - (lineStarts[foundLineNo] - lineStarts[foundLineNo + i]),
        maxLineLength
      );
      result += repeat('  ', options.indent) + padStart((mark.line + i + 1).toString(), lineNoLength) +
        ' | ' + line.str + '\n';
    }
  
    return result.replace(/\n$/, '');
  }


  var mark = {
    string:   str, // omit trailing \0
    position: 1,
    line:     1,
    column:   1
  };

  var t = makeSnippet(mark);
  console.log(t)
```
