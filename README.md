# WebGL Exercise

## Prompt used in this project using GitHub Copilot

1. How to use gl.LINES to illustrate the letters TSA onto the canvas

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WebGL TSA</title>
</head>
<body>
    <canvas id="glCanvas" width="800" height="600"></canvas>
    <script>
        const canvas = document.getElementById('glCanvas');
        let gl = canvas.getContext('webgl');

        if (!gl) {
            console.error('WebGL not supported, falling back on experimental-webgl');
            gl = canvas.getContext('experimental-webgl');
        }

        if (!gl) {
            alert('Your browser does not support WebGL');
        }

        // Vertex shader program
        const vsSource = `
            attribute vec4 aVertexPosition;
            void main(void) {
                gl_Position = aVertexPosition;
            }
        `;

        // Fragment shader program
        const fsSource = `
            void main(void) {
                gl_FragColor = vec4(1.0, 1.0, 1.0, 1.0); // white color
            }
        `;

        // Initialize a shader program; this is where all the lighting
        // for the vertices and so forth is established.
        const shaderProgram = initShaderProgram(gl, vsSource, fsSource);

        // Collect all the info needed to use the shader program.
        const programInfo = {
            program: shaderProgram,
            attribLocations: {
                vertexPosition: gl.getAttribLocation(shaderProgram, 'aVertexPosition'),
            },
        };

        // Here's where we call the routine that builds all the
        // objects we'll be drawing.
        const buffers = initBuffers(gl);

        // Draw the scene
        drawScene(gl, programInfo, buffers);

        // Initialize the buffers we'll need. For this demo, we just
        // have one object -- a simple two-dimensional square.
        function initBuffers(gl) {
            // Create a buffer for the square's positions.
            const positionBuffer = gl.createBuffer();

            // Select the positionBuffer as the one to apply buffer
            // operations to from here out.
            gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);

            // Now create an array of positions for the square.
            const positions = [
                // T
                -0.9,  0.9,  -0.5,  0.9,
                -0.7,  0.9,  -0.7,  0.5,
                // S
                -0.4,  0.9,  -0.2,  0.9,
                -0.4,  0.9,  -0.4,  0.7,
                -0.4,  0.7,  -0.2,  0.7,
                -0.2,  0.7,  -0.2,  0.5,
                -0.2,  0.5,  -0.4,  0.5,
                // A
                0.1,  0.5,   0.3,  0.9,
                0.3,  0.9,   0.5,  0.5,
                0.2,  0.7,   0.4,  0.7,
            ];

            // Now pass the list of positions into WebGL to build the
            // shape. We do this by creating a Float32Array from the
            // JavaScript array, then use it to fill the current buffer.
            gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);

            return {
                position: positionBuffer,
            };
        }

        function drawScene(gl, programInfo, buffers) {
            // Clear the canvas before we start drawing on it.
            gl.clearColor(0.0, 0.0, 0.0, 1.0);  // Clear to black, fully opaque
            gl.clearDepth(1.0);                 // Clear everything
            gl.enable(gl.DEPTH_TEST);           // Enable depth testing
            gl.depthFunc(gl.LEQUAL);            // Near things obscure far things

            // Clear the canvas before we start drawing on it.
            gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);

            // Tell WebGL how to pull out the positions from the position
            // buffer into the vertexPosition attribute.
            {
                const numComponents = 2;  // pull out 2 values per iteration
                const type = gl.FLOAT;    // the data in the buffer is 32bit floats
                const normalize = false;  // don't normalize
                const stride = 0;         // how many bytes to get from one set of values to the next
                                            // 0 = use type and numComponents above
                const offset = 0;         // how many bytes inside the buffer to start from
                gl.bindBuffer(gl.ARRAY_BUFFER, buffers.position);
                gl.vertexAttribPointer(
                    programInfo.attribLocations.vertexPosition,
                    numComponents,
                    type,
                    normalize,
                    stride,
                    offset);
                gl.enableVertexAttribArray(
                    programInfo.attribLocations.vertexPosition);
            }

            // Tell WebGL to use our program when drawing
            gl.useProgram(programInfo.program);

            // Draw the lines
            {
                const offset = 0;
                const vertexCount = 18; // 9 lines, 2 vertices each
                gl.drawArrays(gl.LINES, offset, vertexCount);
            }
        }

        // Initialize a shader program, so WebGL knows how to draw our data
        function initShaderProgram(gl, vsSource, fsSource) {
            const vertexShader = loadShader(gl, gl.VERTEX_SHADER, vsSource);
            const fragmentShader = loadShader(gl, gl.FRAGMENT_SHADER, fsSource);

            // Create the shader program
            const shaderProgram = gl.createProgram();
            gl.attachShader(shaderProgram, vertexShader);
            gl.attachShader(shaderProgram, fragmentShader);
            gl.linkProgram(shaderProgram);

            // If creating the shader program failed, alert
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                console.error('Unable to initialize the shader program: ' + gl.getProgramInfoLog(shaderProgram));
                return null;
            }

            return shaderProgram;
        }

        // creates a shader of the given type, uploads the source and
        // compiles it.
        function loadShader(gl, type, source) {
            const shader = gl.createShader(type);

            // Send the source to the shader object
            gl.shaderSource(shader, source);

            // Compile the shader program
            gl.compileShader(shader);

            // See if it compiled successfully
            if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
                console.error('An error occurred compiling the shaders: ' + gl.getShaderInfoLog(shader));
                gl.deleteShader(shader);
                return null;
            }

            return shader;
        }
    </script>
</body>
</html>
```

2. Make the second scene to also use gl.TRIANGLES

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>gl.LINES and gl.TRIANGLES</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            overflow: hidden;
            width: 100%;
            height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        canvas {
            width: 80%;
            height: 80%;
            background-color: #000; /* Initial background color */
        }
    </style>
</head>
<body>
    <canvas id="glCanvas"></canvas>
    <script>
        const canvas = document.getElementById('glCanvas');
        let gl = canvas.getContext('webgl');

        if (!gl) {
            console.error('WebGL not supported, falling back on experimental-webgl');
            gl = canvas.getContext('experimental-webgl');
        }

        if (!gl) {
            alert('Your browser does not support WebGL');
        }

        // Vertex shader program
        const vsSource = `
            attribute vec4 aVertexPosition;
            void main(void) {
                gl_Position = aVertexPosition;
            }
        `;

        // Fragment shader program
        const fsSource = `
            void main(void) {
                gl_FragColor = vec4(0.0, 1.0, 0.0, 1.0); // Green color for letters
            }
        `;

        // Initialize a shader program; this is where all the lighting
        // for the vertices and so forth is established.
        const shaderProgram = initShaderProgram(gl, vsSource, fsSource);

        // Collect all the info needed to use the shader program.
        const programInfo = {
            program: shaderProgram,
            attribLocations: {
                vertexPosition: gl.getAttribLocation(shaderProgram, 'aVertexPosition'),
            },
        };

        // Here's where we call the routine that builds all the
        // objects we'll be drawing.
        const buffers = initTriangleBuffers(gl);

        // Draw the scene
        drawTriangles(gl, programInfo, buffers);

        // Clear the canvas and redraw after 3 seconds
        setTimeout(() => {
            // Clear the canvas
            gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);

            // Change the canvas background color to Fuchsia
            canvas.style.backgroundColor = '#FF00FF';

            // Redraw the first three letters of my name using gl.TRIANGLES
            const newBuffers = initTriangleBuffers(gl);
            drawTriangles(gl, programInfo, newBuffers);
        }, 3000);

        function initTriangleBuffers(gl) {
            // Create a buffer for the triangle's positions.
            const positionBuffer = gl.createBuffer();

            // Select the positionBuffer as the one to apply buffer
            // operations to from here out.
            gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);

            // Now create an array of positions for the triangles.
            const positions = [
                // T
                -0.9,  0.9,  -0.7,  0.9,  -0.8,  0.7,
                -0.8,  0.7,  -0.8,  0.5,  -0.9,  0.5,
                -0.8,  0.7,  -0.7,  0.9,  -0.8,  0.5,
                // S
                -0.6,  0.9,  -0.4,  0.9,  -0.6,  0.7,
                -0.6,  0.7,  -0.4,  0.9,  -0.4,  0.7,
                -0.6,  0.7,  -0.4,  0.7,  -0.6,  0.5,
                -0.6,  0.5,  -0.4,  0.7,  -0.4,  0.5,
                // A
                -0.2,  0.5,  -0.1,  0.9,  0.0,  0.5,
                -0.15, 0.7,  -0.05, 0.7,  -0.1,  0.9,
            ];

            // Now pass the list of positions into WebGL to build the
            // shape. We do this by creating a Float32Array from the
            // JavaScript array, then use it to fill the current buffer.
            gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);

            return {
                position: positionBuffer,
            };
        }

        function drawTriangles(gl, programInfo, buffers) {
            // Clear the canvas before we start drawing on it.
            gl.clearColor(0.0, 0.0, 0.0, 1.0);  // Clear to black, fully opaque
            gl.clearDepth(1.0);                 // Clear everything
            gl.enable(gl.DEPTH_TEST);           // Enable depth testing
            gl.depthFunc(gl.LEQUAL);            // Near things obscure far things

            // Clear the canvas before we start drawing on it.
            gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);

            // Tell WebGL how to pull out the positions from the position
            // buffer into the vertexPosition attribute.
            {
                const numComponents = 2;  // pull out 2 values per iteration
                const type = gl.FLOAT;    // the data in the buffer is 32bit floats
                const normalize = false;  // don't normalize
                const stride = 0;         // how many bytes to get from one set of values to the next
                                            // 0 = use type and numComponents above
                const offset = 0;         // how many bytes inside the buffer to start from
                gl.bindBuffer(gl.ARRAY_BUFFER, buffers.position);
                gl.vertexAttribPointer(
                    programInfo.attribLocations.vertexPosition,
                    numComponents,
                    type,
                    normalize,
                    stride,
                    offset);
                gl.enableVertexAttribArray(
                    programInfo.attribLocations.vertexPosition);
            }

            // Tell WebGL to use our program when drawing
            gl.useProgram(programInfo.program);

            // Draw the triangles
            {
                const offset = 0;
                const vertexCount = 18; // 6 triangles, 3 vertices each
                gl.drawArrays(gl.TRIANGLES, offset, vertexCount);
            }
        }

        // Initialize a shader program, so WebGL knows how to draw our data
        function initShaderProgram(gl, vsSource, fsSource) {
            const vertexShader = loadShader(gl, gl.VERTEX_SHADER, vsSource);
            const fragmentShader = loadShader(gl, gl.FRAGMENT_SHADER, fsSource);

            // Create the shader program
            const shaderProgram = gl.createProgram();
            gl.attachShader(shaderProgram, vertexShader);
            gl.attachShader(shaderProgram, fragmentShader);
            gl.linkProgram(shaderProgram);

            // If creating the shader program failed, alert
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                console.error('Unable to initialize the shader program: ' + gl.getProgramInfoLog(shaderProgram));
                return null;
            }

            return shaderProgram;
        }

        // creates a shader of the given type, uploads the source and
        // compiles it.
        function loadShader(gl, type, source) {
            const shader = gl.createShader(type);

            // Send the source to the shader object
            gl.shaderSource(shader, source);

            // Compile the shader program
            gl.compileShader(shader);

            // See if it compiled successfully
            if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
                console.error('An error occurred compiling the shaders: ' + gl.getShaderInfoLog(shader));
                gl.deleteShader(shader);
                return null;
            }

            return shader;
        }
    </script>
</body>
</html>
```

3. Make it so that it cycles between the first scene and the second scene

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>gl.LINES and gl.TRIANGLES</title>
    <style>
        body, html {
            margin: 0;
            padding: 0;
            overflow: hidden;
            width: 100%;
            height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        canvas {
            width: 80%;
            height: 80%;
            background-color: #000; /* Initial background color */
        }
    </style>
</head>
<body>
    <canvas id="glCanvas"></canvas>
    <script>
        const canvas = document.getElementById('glCanvas');
        let gl = canvas.getContext('webgl');

        if (!gl) {
            console.error('WebGL not supported, falling back on experimental-webgl');
            gl = canvas.getContext('experimental-webgl');
        }

        if (!gl) {
            alert('Your browser does not support WebGL');
        }

        // Vertex shader program
        const vsSource = `
            attribute vec4 aVertexPosition;
            void main(void) {
                gl_Position = aVertexPosition;
            }
        `;

        // Fragment shader program
        const fsSource = `
            void main(void) {
                gl_FragColor = vec4(0.0, 1.0, 0.0, 1.0); // Green color for letters
            }
        `;

        // Initialize a shader program; this is where all the lighting
        // for the vertices and so forth is established.
        const shaderProgram = initShaderProgram(gl, vsSource, fsSource);

        // Collect all the info needed to use the shader program.
        const programInfo = {
            program: shaderProgram,
            attribLocations: {
                vertexPosition: gl.getAttribLocation(shaderProgram, 'aVertexPosition'),
            },
        };

        // Initialize buffers for both scenes
        const lineBuffers = initLineBuffers(gl);
        const triangleBuffers = initTriangleBuffers(gl);

        // Function to draw the initial scene using gl.LINES
        function drawInitialScene() {
            // Clear the canvas
            gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);

            // Set the canvas background color to black
            canvas.style.backgroundColor = '#000';

            // Draw the TSA letters using gl.LINES
            drawLines(gl, programInfo, lineBuffers);
        }

        // Function to draw the second scene using gl.TRIANGLES
        function drawSecondScene() {
            // Clear the canvas
            gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);

            // Change the canvas background color to Fuchsia
            canvas.style.backgroundColor = '#FF00FF';

            // Draw the TSA letters using gl.TRIANGLES
            drawTriangles(gl, programInfo, triangleBuffers);
        }

        // Cycle between the two scenes every 3 seconds
        let isInitialScene = true;
        setInterval(() => {
            if (isInitialScene) {
                drawSecondScene();
            } else {
                drawInitialScene();
            }
            isInitialScene = !isInitialScene;
        }, 3000);

        // Draw the initial scene
        drawInitialScene();

        function initLineBuffers(gl) {
            // Create a buffer for the line's positions.
            const positionBuffer = gl.createBuffer();

            // Select the positionBuffer as the one to apply buffer
            // operations to from here out.
            gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);

            // Now create an array of positions for the lines.
            const positions = [
                // T
                -0.9,  0.9,  -0.5,  0.9,
                -0.7,  0.9,  -0.7,  0.5,
                -0.75, 0.9,  -0.65, 0.9,  // Extra horizontal line for thickness
                -0.7,  0.9,  -0.7,  0.7,  // Extra vertical line for thickness
            
                // S
                -0.4,  0.9,  -0.2,  0.9,
                -0.4,  0.9,  -0.4,  0.7,
                -0.4,  0.7,  -0.2,  0.7,
                -0.2,  0.7,  -0.2,  0.5,
                -0.2,  0.5,  -0.4,  0.5,
                -0.4,  0.5,  -0.4,  0.3,  // Extra vertical line for thickness
                -0.4,  0.3,  -0.2,  0.3,  // Extra horizontal line for thickness
            
                // A
                0.1,  0.5,   0.3,  0.9,
                0.3,  0.9,   0.5,  0.5,
                0.2,  0.7,   0.4,  0.7,
                0.1,  0.5,   0.2,  0.5,  // Extra horizontal line for thickness
                0.4,  0.5,   0.5,  0.5,  // Extra horizontal line for thickness
                0.3,  0.9,   0.3,  0.7,  // Extra vertical line for thickness
            ];

            // Now pass the list of positions into WebGL to build the
            // shape. We do this by creating a Float32Array from the
            // JavaScript array, then use it to fill the current buffer.
            gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);

            return {
                position: positionBuffer,
            };
        }

        function initTriangleBuffers(gl) {
            // Create a buffer for the triangle's positions.
            const positionBuffer = gl.createBuffer();

            // Select the positionBuffer as the one to apply buffer
            // operations to from here out.
            gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);

            // Now create an array of positions for the triangles.
            const positions = [
                // T
                -0.9,  0.9,  -0.7,  0.9,  -0.8,  0.7,
                -0.8,  0.7,  -0.9,  0.5,  -0.8,  0.5,
                -0.8,  0.7,  -0.7,  0.9,  -0.8,  0.5,
                // S
                -0.6,  0.9,  -0.4,  0.9,  -0.6,  0.7,
                -0.6,  0.7,  -0.4,  0.9,  -0.4,  0.7,
                -0.6,  0.7,  -0.4,  0.7,  -0.6,  0.5,
                -0.6,  0.5,  -0.4,  0.7,  -0.4,  0.5,
                // A
                -0.2,  0.5,  -0.1,  0.9,  0.0,  0.5,
                -0.2,  0.5,  -0.1,  0.7,  0.0,  0.5,
                -0.15, 0.7,  -0.05, 0.7,  -0.1,  0.9,
            ];

            // Now pass the list of positions into WebGL to build the
            // shape. We do this by creating a Float32Array from the
            // JavaScript array, then use it to fill the current buffer.
            gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);

            return {
                position: positionBuffer,
            };
        }

        function drawLines(gl, programInfo, buffers) {
            // Clear the canvas before we start drawing on it.
            gl.clearColor(0.0, 0.0, 0.0, 1.0);  // Clear to black, fully opaque
            gl.clearDepth(1.0);                 // Clear everything
            gl.enable(gl.DEPTH_TEST);           // Enable depth testing
            gl.depthFunc(gl.LEQUAL);            // Near things obscure far things

            // Clear the canvas before we start drawing on it.
            gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);

            // Tell WebGL how to pull out the positions from the position
            // buffer into the vertexPosition attribute.
            {
                const numComponents = 2;  // pull out 2 values per iteration
                const type = gl.FLOAT;    // the data in the buffer is 32bit floats
                const normalize = false;  // don't normalize
                const stride = 0;         // how many bytes to get from one set of values to the next
                                            // 0 = use type and numComponents above
                const offset = 0;         // how many bytes inside the buffer to start from
                gl.bindBuffer(gl.ARRAY_BUFFER, buffers.position);
                gl.vertexAttribPointer(
                    programInfo.attribLocations.vertexPosition,
                    numComponents,
                    type,
                    normalize,
                    stride,
                    offset);
                gl.enableVertexAttribArray(
                    programInfo.attribLocations.vertexPosition);
            }

            // Tell WebGL to use our program when drawing
            gl.useProgram(programInfo.program);

            // Draw the lines
            {
                const offset = 0;
                const vertexCount = 27; // 9 lines, 3 vertices each
                gl.drawArrays(gl.LINES, offset, vertexCount);
            }
        }

        function drawTriangles(gl, programInfo, buffers) {
            // Clear the canvas before we start drawing on it.
            gl.clearColor(1.0, 0.0, 1.0, 1.0);  // Clear to fuchsia, fully opaque
            gl.clearDepth(1.0);                 // Clear everything
            gl.enable(gl.DEPTH_TEST);           // Enable depth testing
            gl.depthFunc(gl.LEQUAL);            // Near things obscure far things

            // Clear the canvas before we start drawing on it.
            gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);

            // Tell WebGL how to pull out the positions from the position
            // buffer into the vertexPosition attribute.
            {
                const numComponents = 2;  // pull out 2 values per iteration
                const type = gl.FLOAT;    // the data in the buffer is 32bit floats
                const normalize = false;  // don't normalize
                const stride = 0;         // how many bytes to get from one set of values to the next
                                            // 0 = use type and numComponents above
                const offset = 0;         // how many bytes inside the buffer to start from
                gl.bindBuffer(gl.ARRAY_BUFFER, buffers.position);
                gl.vertexAttribPointer(
                    programInfo.attribLocations.vertexPosition,
                    numComponents,
                    type,
                    normalize,
                    stride,
                    offset);
                gl.enableVertexAttribArray(
                    programInfo.attribLocations.vertexPosition);
            }

            // Tell WebGL to use our program when drawing
            gl.useProgram(programInfo.program);

            // Draw the triangles
            {
                const offset = 0;
                const vertexCount = 27; // 9 triangles, 3 vertices each
                gl.drawArrays(gl.TRIANGLES, offset, vertexCount);
            }
        }

        // Initialize a shader program, so WebGL knows how to draw our data
        function initShaderProgram(gl, vsSource, fsSource) {
            const vertexShader = loadShader(gl, gl.VERTEX_SHADER, vsSource);
            const fragmentShader = loadShader(gl, gl.FRAGMENT_SHADER, fsSource);

            // Create the shader program
            const shaderProgram = gl.createProgram();
            gl.attachShader(shaderProgram, vertexShader);
            gl.attachShader(shaderProgram, fragmentShader);
            gl.linkProgram(shaderProgram);

            // If creating the shader program failed, alert
            if (!gl.getProgramParameter(shaderProgram, gl.LINK_STATUS)) {
                console.error('Unable to initialize the shader program: ' + gl.getProgramInfoLog(shaderProgram));
                return null;
            }

            return shaderProgram;
        }

        // creates a shader of the given type, uploads the source and
        // compiles it.
        function loadShader(gl, type, source) {
            const shader = gl.createShader(type);

            // Send the source to the shader object
            gl.shaderSource(shader, source);

            // Compile the shader program
            gl.compileShader(shader);

            // See if it compiled successfully
            if (!gl.getShaderParameter(shader, gl.COMPILE_STATUS)) {
                console.error('An error occurred compiling the shaders: ' + gl.getShaderInfoLog(shader));
                gl.deleteShader(shader);
                return null;
            }

            return shader;
        }
    </script>
</body>
</html>
```