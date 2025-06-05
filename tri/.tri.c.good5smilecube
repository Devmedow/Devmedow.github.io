#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <math.h>
#include <GL/gl.h>
#include <GLFW/glfw3.h>
#include <emscripten/emscripten.h>
#include <emscripten/html5.h>

GLFWwindow* window;
float clearColor[3] = {0.2f, 0.3f, 0.4f};
float angle = 0.0f;

// Change background color randomly
void randomize_clear_color() {
    clearColor[0] = (float)(rand() % 1000) / 1000.0f;
    clearColor[1] = (float)(rand() % 1000) / 1000.0f;
    clearColor[2] = (float)(rand() % 1000) / 1000.0f;
}

void mouse_button_callback(GLFWwindow* window, int button, int action, int mods) {
    if (action == GLFW_PRESS) {
        randomize_clear_color();
    }
}

void key_callback(GLFWwindow* window, int key, int scancode, int action, int mods) {
    if (action == GLFW_PRESS) {
        randomize_clear_color();
    }
}

// Helper to set perspective projection matrix (like gluPerspective)
void set_perspective(float fov_y_deg, float aspect, float z_near, float z_far) {
    float fov_rad = fov_y_deg * 3.14159265f / 180.0f;
    float f = 1.0f / tanf(fov_rad / 2.0f);
    float nf = 1.0f / (z_near - z_far);

    GLfloat proj[16] = {0};

    if(aspect>1) {
        proj[0]  = f / aspect;
        proj[5]  = f;
    } else {
        proj[0]  = f;
        proj[5]  = f * aspect;
    }
    proj[10] = (z_far + z_near) * nf;
    proj[11] = -1.0f;
    proj[14] = (2.0f * z_far * z_near) * nf;

    glMatrixMode(GL_PROJECTION);
    glLoadMatrixf(proj);
}

GLuint tex;
const unsigned char smiley[8][8] = {
    {1,1,1,1,1,1,1,1},
    {1,0,0,1,1,0,0,1},
    {1,0,0,1,1,0,0,1},
    {1,0,0,1,1,0,0,1},
    {1,1,1,1,1,1,1,1},
    {1,0,1,1,1,1,0,1},
    {1,0,0,0,0,0,0,1},
    {1,1,1,1,1,1,1,1},
};

void upload_texture() {
    unsigned char pixels[8 * 8 * 4];
    for (int y = 0; y < 8; ++y)
        for (int x = 0; x < 8; ++x) {
            int i = (y * 8 + x) * 4;
            unsigned char c = smiley[y][x] ? 255 : 0;
            pixels[i+0] = c;
            pixels[i+1] = c;
            pixels[i+2] = c;
            pixels[i+3] = 255;
        }

    glGenTextures(1, &tex);
    glBindTexture(GL_TEXTURE_2D, tex);
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGBA, 8, 8, 0, GL_RGBA, GL_UNSIGNED_BYTE, pixels);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);
    glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
}

// Draw a colored cube using immediate mode
void draw_cube() {
    glEnable(GL_TEXTURE_2D);
    glBindTexture(GL_TEXTURE_2D,tex);

    glBegin(GL_QUADS);
    // Front (z = 0.5)
    glTexCoord2f(0,0); glColor3f(1,0,0); glVertex3f(-0.5f, -0.5f,  0.5f);
    glTexCoord2f(1,0); glColor3f(1,0.5,0); glVertex3f( 0.5f, -0.5f,  0.5f);
    glTexCoord2f(1,1); glColor3f(1,0,0.5); glVertex3f( 0.5f,  0.5f,  0.5f);
    glTexCoord2f(0,1); glColor3f(1,0.5,0); glVertex3f(-0.5f,  0.5f,  0.5f);

    // Back (z = -0.5)
    glTexCoord2f(0,0); glColor3f(0,1,0); glVertex3f(-0.5f, -0.5f, -0.5f);
    glTexCoord2f(1,0); glColor3f(0,1,0.5); glVertex3f(-0.5f,  0.5f, -0.5f);
    glTexCoord2f(1,1); glColor3f(0.5,1,0); glVertex3f( 0.5f,  0.5f, -0.5f);
    glTexCoord2f(0,1); glColor3f(0,1,0.5); glVertex3f( 0.5f, -0.5f, -0.5f);

    // Left (x = -0.5)
    glTexCoord2f(0,0); glColor3f(0,0,1); glVertex3f(-0.5f, -0.5f, -0.5f);
    glTexCoord2f(1,0); glColor3f(0,0.5,1); glVertex3f(-0.5f, -0.5f,  0.5f);
    glTexCoord2f(1,1); glColor3f(0.5,0,1); glVertex3f(-0.5f,  0.5f,  0.5f);
    glTexCoord2f(0,1); glColor3f(0,0.5,1); glVertex3f(-0.5f,  0.5f, -0.5f);

    // Right (x = 0.5)
    glTexCoord2f(0,0); glColor3f(1,1,0); glVertex3f( 0.5f, -0.5f, -0.5f);
    glTexCoord2f(1,0); glColor3f(1,1,0.5); glVertex3f( 0.5f,  0.5f, -0.5f);
    glTexCoord2f(1,1); glColor3f(1,1,0); glVertex3f( 0.5f,  0.5f,  0.5f);
    glTexCoord2f(0,1); glColor3f(1,1,0.5); glVertex3f( 0.5f, -0.5f,  0.5f);

    // Top (y = 0.5)
    glTexCoord2f(0,0); glColor3f(1,0,1); glVertex3f(-0.5f,  0.5f, -0.5f);
    glTexCoord2f(1,0); glColor3f(1,0.5,1); glVertex3f(-0.5f,  0.5f,  0.5f);
    glTexCoord2f(1,1); glColor3f(1,0,1); glVertex3f( 0.5f,  0.5f,  0.5f);
    glTexCoord2f(0,1); glColor3f(1,0.5,1); glVertex3f( 0.5f,  0.5f, -0.5f);

    // Bottom (y = -0.5)
    glTexCoord2f(0,0); glColor3f(0,1,1); glVertex3f(-0.5f, -0.5f, -0.5f);
    glTexCoord2f(1,0); glColor3f(0.5,1,1); glVertex3f( 0.5f, -0.5f, -0.5f);
    glTexCoord2f(1,1); glColor3f(0,1,1); glVertex3f( 0.5f, -0.5f,  0.5f);
    glTexCoord2f(0,1); glColor3f(0.5,1,1); glVertex3f(-0.5f, -0.5f,  0.5f);
    glEnd();
}

void render() {
    int width, height;
    emscripten_get_canvas_element_size("#canvas", &width, &height);
    glViewport(0, 0, width, height);

    glClearColor(clearColor[0], clearColor[1], clearColor[2], 1.0f);
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

    // Enable depth test for correct 3D rendering
    glEnable(GL_DEPTH_TEST);

    // Setup projection matrix
    float aspect = (float)width / (float)height;
    set_perspective(60.0f, aspect, 0.1f, 100.0f);

    // Setup model-view matrix
    glMatrixMode(GL_MODELVIEW);
    glLoadIdentity();

    // Move camera backwards so cube is visible
    glTranslatef(0.0f, 0.0f, -2.0f);

    // Rotate cube
    glRotatef(angle, 1.0f, 1.0f, 0.0f);
    glRotatef(angle*0.4, 0.0f, 0.0f, 1.0f);
    glRotatef(angle*0.1, 0.0f, 1.0f, 1.0f);

    draw_cube();

    glfwSwapBuffers(window);
    glfwPollEvents();

    angle += 1.0f;
    //if (angle >= 360.0f) angle -= 360.0f;
}

int main() {
    srand(time(NULL));

    if (!glfwInit()) {
        fprintf(stderr, "Failed to initialize GLFW\n");
        return 1;
    }

    int width, height;
    emscripten_get_canvas_element_size("#canvas", &width, &height);
    window = glfwCreateWindow(width, height, "Rotating Cube", NULL, NULL);
    if (!window) {
        fprintf(stderr, "Failed to create GLFW window\n");
        glfwTerminate();
        return 1;
    }

    glfwMakeContextCurrent(window);

    glfwSetMouseButtonCallback(window, mouse_button_callback);
    glfwSetKeyCallback(window, key_callback);

    upload_texture();

    emscripten_set_main_loop(render, 0, 1);
    return 0;
}
