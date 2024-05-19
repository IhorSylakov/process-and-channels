# Process-and-channels

## Functional Description

### General Requirements
1. The application must have buttons to enable the display of each of the four types of processes:
    <table>
      <tr>
        <td align="center">
          <img src="https://raw.githubusercontent.com/IhorSylakov/process-and-channels/825e469256fbed9e099715971a19eb1dc329a0e1/src/assets/ongoing.svg" alt="Ongoing" width="100"/><br>
          <sub>Ongoing</sub>
        </td>
        <td align="center">
          <img src="https://raw.githubusercontent.com/IhorSylakov/process-and-channels/825e469256fbed9e099715971a19eb1dc329a0e1/src/assets/linear.svg" alt="Linear" width="100"/><br>
          <sub>Linear</sub>
        </td>
        <td align="center">
          <img src="https://raw.githubusercontent.com/IhorSylakov/process-and-channels/825e469256fbed9e099715971a19eb1dc329a0e1/src/assets/non-linear.svg" alt="Non-linear" width="100"/><br>
          <sub>Non-linear</sub>
        </td>
        <td align="center">
          <img src="https://raw.githubusercontent.com/IhorSylakov/process-and-channels/825e469256fbed9e099715971a19eb1dc329a0e1/src/assets/bidirectional.svg" alt="Bidirectional" width="100"/><br>
          <sub>Bidirectional</sub>
        </td>
      </tr>
    </table>

2. Each path should have channels (from 1 to 10), with a default of 3 channels, and the number of channels can be adjusted via an input field.
3. When switching between process types, the number of channels should be as indicated in the input field.
4. Channels should have a round icon and a name for channel next to it. The placement of the text depends on the type of process and the number of channels.


### Ongoing Process
1. The path has a circular or oval shape.
2. The size of the path can be adjusted using controllers on the right and bottom of the display area.
3. There is an arrow at the top of the path.
4. Channels are placed equidistantly from each other and from the arrow.
5. Name text placement:
    - In the top right sector: text to the top right of the icon.
    - In the bottom right sector: text to the bottom right of the icon.
    - In the bottom left sector: text to the bottom left of the icon.
    - In the top left sector: text to the top left of the icon.

### Linear Process
1. The path has the shape of a horizontal arrow pointing to the right.
2. The arrow is at the right end of the line, the first channel is at the left end of the line, and the remaining channels are spaced evenly.
3. The size of the display area can only be adjusted horizontally.
4. Name text placement:
    - For odd-numbered channels, the text is below.
    - For even-numbered channels, the text is above.

### Non-linear Process
1. The path has a zigzag shape with an arrow pointing to the right.
2. Channels are placed at the bends of the zigzag as follows:
    - The last channel is always at the top bend.
    - Odd-numbered channels (starting from the end) are at the top bends, even-numbered channels are at the bottom bends.
3. The size of the display area can be adjusted in width.
4. Name text placement:
    - For channels at the top bends, the text is above.
    - For channels at the bottom bends, the text is below.

### Bidirectional Process
1. The path has the shape of a horizontal bidirectional arrow (pointing both right and left).
2. Arrows are placed at both ends of the path line.
3. Channels are spaced evenly between the arrows.
4. The size of the display area can be adjusted in width.
5. Name text placement:
    - For odd-numbered channels, the text is below.
    - For even-numbered channels, the text is above.

[Check how it works on page](https://ihorsylakov.github.io/process-and-channels/)

![Alt text](https://github.com/IhorSylakov/IhorSylakov/blob/main/repo-previews/process-and-channels.gif?raw=true "a title")

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```
