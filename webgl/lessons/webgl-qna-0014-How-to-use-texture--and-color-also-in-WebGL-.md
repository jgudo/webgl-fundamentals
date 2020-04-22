Title: How to use texture, and color also in WebGL?
Description:
TOC: qna

# Question:

I'm learning WebGL, and I would like to make a program, where there are colored, and textured objects also. I tried to modify a bit the fragment shader: If the object don't have texture, then it will be colored.

    precision mediump float;

    varying vec4 vColor;
 varying vec2 vTextureCoord;
 
 uniform sampler2D uSampler;

    void main(void) {
     gl_FragColor = vColor;
  gl_FragColor = texture2D(uSampler, vec2(vTextureCoord.s, vTextureCoord.t));
    }

It doesn't works. I get an error: Could not initialize shaders.

# Answer

This may or may not make sense but whether you have a texture or not requires different shaders. To get around this many people use a single pixel white texture when they don't want textures. That way they only need 1 shader to cover both cases. The shader might look something like this

    uniform vec4 u_color;
    uniform sampler2D u_texture;

    varying vec2 v_texCoord;

    void main(void) {
      gl_FragColor = texture2D(u_texture, v_texCoord) * u_color;
    }

Now, in your code you can draw textured like this

    // at init time.
    var whiteColor = new Float32Array([1, 1, 1, 1]);

    // at draw time
    gl.uniform4fv(u_colorLocation, whiteColor);  // use white color
    gl.bindTexture(gl.TEXTURE_2D, someTexture);  // and some texture
    ... draw ...

And to draw un-textured you do this

    // at init time.
    var whiteTexture = gl.createTexture();
    gl.bindTexture(gl.TEXTURE_2D, whiteTexture);
    var whitePixel = new Uint8Array([255, 255, 255, 255]);
    gl.texImage2D(gl.TEXTURE_2D, 0, gl.RGBA, 1, 1, 0, 
                  gl.RGBA, gl.UNSIGNED_BYTE, whitePixel);
 

    // at draw time
    gl.uniform4fv(u_colorLocation, someColor);  // use the color I want
    gl.bindTexture(gl.TEXTURE_2D, whiteTexture);  // use the white texture
    ... draw ...

That also gives you the ability to tint your textures by using a non white color and a texture together.