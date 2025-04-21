# Aprendiendo MCP (Model-Controller-Presenter)

## ¿Qué es MCP?

El patrón de arquitectura MCP (Model-Controller-Presenter) es una variante del conocido MVC (Model-View-Controller) que se centra en mejorar la separación de responsabilidades en aplicaciones, especialmente aquellas con interfaces de usuario complejas.

## Componentes Principales

### 1. Model (Modelo)
Representa los datos y la lógica de negocio de la aplicación:
- Contiene la información con la que trabaja la aplicación
- Implementa la lógica de negocio y validación de datos
- Es independiente de la interfaz de usuario
- Notifica cambios a los componentes interesados (generalmente al Presenter)

### 2. Controller (Controlador)
Actúa como intermediario inicial entre la vista y el modelo:
- Recibe las entradas y eventos del usuario desde la vista
- Decide qué operación realizar en base a la entrada recibida
- Delega la mayoría del trabajo al Presenter
- Es más ligero que en MVC tradicional

### 3. Presenter (Presentador)
Es la pieza clave que diferencia a MCP de MVC:
- Actúa como mediador entre el Model y la View
- Recupera datos del Model y los formatea para mostrarlos en la View
- Contiene la lógica de presentación (cómo mostrar los datos)
- Reacciona a las notificaciones del Model y actualiza la View
- No tiene dependencias directas con componentes de UI específicos

## Flujo de trabajo en MCP

1. El usuario interactúa con la interfaz (View)
2. La View notifica al Controller sobre la acción del usuario
3. El Controller procesa el evento y se comunica con el Presenter
4. El Presenter solicita datos al Model según sea necesario
5. El Model procesa la solicitud y devuelve resultados al Presenter
6. El Presenter formatea los datos y actualiza la View
7. La View muestra la información al usuario

## Ventajas de MCP

- **Mayor testabilidad**: El Presenter no depende directamente de la View, lo que facilita las pruebas unitarias
- **Separación clara de responsabilidades**: Cada componente tiene un rol bien definido
- **Mantenibilidad mejorada**: Los cambios en la interfaz afectan mínimamente al resto del sistema
- **Reutilización**: Los Presenters pueden utilizarse con diferentes implementaciones de View

## Diferencias con MVC

| Aspecto | MVC | MCP |
|---------|-----|-----|
| Comunicación con la View | El Controller actualiza directamente el Model y la View | El Presenter se encarga de la lógica de presentación y actualización de la View |
| Dependencias | La View puede tener acceso directo al Model | La View normalmente solo se comunica con el Presenter |
| Lógica de presentación | Distribuida entre Controller y View | Centralizada en el Presenter |
| Testabilidad | Más difícil por la dependencia directa con la UI | Mejor debido a la abstracción de la UI |

## Implementación Típica

```java
// Interfaz para la Vista
interface UserView {
    void showUserData(String name, String email);
    void showError(String message);
}

// Modelo
class UserModel {
    private String name;
    private String email;
    
    // Constructor, getters, setters y métodos de negocio
    public void saveUser() {
        // Lógica para guardar usuario en la base de datos
    }
}

// Presenter
class UserPresenter {
    private UserModel model;
    private UserView view;
    
    public UserPresenter(UserModel model, UserView view) {
        this.model = model;
        this.view = view;
    }
    
    public void loadUserData() {
        // Obtiene datos del modelo y actualiza la vista
        view.showUserData(model.getName(), model.getEmail());
    }
    
    public void updateUserData(String name, String email) {
        model.setName(name);
        model.setEmail(email);
        model.saveUser();
        loadUserData(); // Actualiza la vista
    }
}

// Controller
class UserController {
    private UserPresenter presenter;
    
    public UserController(UserPresenter presenter) {
        this.presenter = presenter;
    }
    
    public void onSaveButtonClick(String name, String email) {
        presenter.updateUserData(name, email);
    }
    
    public void onViewInitialized() {
        presenter.loadUserData();
    }
}
```

## Casos de Uso Recomendados

MCP es especialmente útil en:
- Aplicaciones con interfaces de usuario complejas
- Proyectos que requieren pruebas unitarias extensivas
- Sistemas que necesitan distintas presentaciones para los mismos datos
- Aplicaciones móviles y web que requieren reutilización de lógica de negocio

## Conclusión

El patrón MCP ofrece una estructura robusta para aplicaciones que necesitan una clara separación de responsabilidades. Aunque implica más clases que MVC, la inversión se compensa con código más mantenible, testable y escalable.
