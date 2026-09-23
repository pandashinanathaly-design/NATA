
/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 * Click nbfs://nbhost/SystemFileSystem/Templates/Classes/Class.java to edit this template
 */
package libro;
import java.sql.*;
import javax.swing.JOptionPane;
/**
 *
 * @author Admin
 */
public class conexion {
    public static Connection conectar() {
        Connection conn = null;
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            conn = DriverManager.getConnection("jdbc:mysql://localhost/biblioteca", "root", "");
            JOptionPane.showMessageDialog(null, "ok esta conectado");
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(null, ex);

        }
        return conn;
    }
}


*******************librodao**********************
/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 * Click nbfs://nbhost/SystemFileSystem/Templates/Classes/Class.java to edit this template
 */
package libro;
import java.sql.*;
import javax.swing.table.DefaultTableModel;

/**
 *
 * @author Admin
 */
public class LibroDao {
    public void cargarAutores(javax.swing.JComboBox combo) {
        try (Connection con = conexion.conectar(); Statement st = con.createStatement(); ResultSet rs = st.executeQuery("SELECT nombre_autor FROM autor")) {
            combo.removeAllItems();
            while (rs.next()) combo.addItem(rs.getString(1));
        } catch (Exception e) {}
    }

    public boolean insertar(String tit, int anio, String aut) {
        String sql = "INSERT INTO libros (id_libro, titulo, anio_publicacion, id_autor) SELECT ?, ?, ?, id_autor FROM autor WHERE nombre_autor = ?";
        try (Connection con = conexion.conectar(); PreparedStatement ps = con.prepareStatement(sql)) {
            ps.setString(1, "L-" + System.currentTimeMillis());
            ps.setString(2, tit);
            ps.setInt(3, anio);
            ps.setString(4, aut);
            return ps.executeUpdate() > 0;
        } catch (Exception e) { return false; }
    }

    public DefaultTableModel buscar(String aut) {
        DefaultTableModel m = new DefaultTableModel(new String[]{"Código", "Título", "Año", "Autor"}, 0);
        String sql = "SELECT l.id_libro, l.titulo, l.anio_publicacion, a.nombre_autor FROM libros l JOIN autor a ON l.id_autor = a.id_autor WHERE a.nombre_autor = ?";
        try (Connection con = conexion.conectar(); PreparedStatement ps = con.prepareStatement(sql)) {
            ps.setString(1, aut);
            ResultSet rs = ps.executeQuery();
            while (rs.next()) m.addRow(new Object[]{rs.getString(1), rs.getString(2), rs.getInt(3), rs.getString(4)});
        } catch (Exception e) {}
        return m;
    }
}

****prueba*****************
public pru() {
        initComponents();
        new LibroDao().cargarAutores(jComboAutor);
    }
    public void guardar() {
        try {
            LibroDao dao = new LibroDao();
            String titulo = jTextTitulo.getText();
            int anio = Integer.parseInt(jTextAnio.getText());
            String autor = jComboAutor.getSelectedItem().toString();

            if (dao.insertar(titulo, anio, autor)) {
                javax.swing.JOptionPane.showMessageDialog(this, "Guardado con éxito");
                jTextTitulo.setText("");
                jTextAnio.setText("");
            } else {
                javax.swing.JOptionPane.showMessageDialog(this, "Error al guardar");
            }
        } catch (NumberFormatException e) {
            javax.swing.JOptionPane.showMessageDialog(this, "El año debe ser un número entero.");
        }
    }

    public void buscar() {
        LibroDao dao = new LibroDao();
        String autorSeleccionado = jComboAutor.getSelectedItem().toString();
        jTablalibros.setModel(dao.buscar(autorSeleccionado));
    }





    *****************************USUARIO DAO**********************
    /*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 * Click nbfs://nbhost/SystemFileSystem/Templates/Classes/Class.java to edit this template
 */
package practica;
import java.sql.*;
import javax.swing.table.DefaultTableModel;
/**
 *
 * @author Admin
 */
public class UsuarioDAo {
    public boolean validarYRegistrar(String u, String p) {
        try (Connection con = Conexion.conectar();
             PreparedStatement ps = con.prepareStatement
        ("SELECT id_usuario FROM usuarios WHERE usuario=? AND clave=?")) {
            ps.setString(1, u); 
            ps.setString(2, p);
            ResultSet rs = ps.executeQuery();
            
            if (rs.next()) {
                int idUsuario = rs.getInt("id_usuario");
                PreparedStatement ps2 = con.prepareStatement("INSERT INTO "
                        + "ingresos (id_usuario) VALUES (?)");
                ps2.setInt(1, idUsuario);
                ps2.executeUpdate();
                return true;
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return false;
    }

    public DefaultTableModel obtenerIngresos() {
        DefaultTableModel modelo = new DefaultTableModel(new String[]{"ID Ingreso", 
            "ID Usuario", "Usuario", "Fecha"}, 0);
        String sql = "SELECT i.id_ingreso, u.id_usuario, u.usuario, i.fecha "
                + "FROM ingresos i JOIN usuarios u ON i.id_usuario = u.id_usuario";
        
        try (Connection con = Conexion.conectar();
             Statement st = con.createStatement();
             ResultSet rs = st.executeQuery(sql)) {
            while (rs.next()) {
                modelo.addRow(new Object[]{
                    rs.getInt("id_ingreso"), 
                    rs.getInt("id_usuario"), 
                    rs.getString("usuario"), 
                    rs.getTimestamp("fecha")
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return modelo;
    }
    public boolean registrarNuevoUsuario(String u, String p) {
    try (Connection con = Conexion.conectar();
         PreparedStatement ps = con.prepareStatement("INSERT "
                 + "INTO usuarios (usuario, clave) VALUES (?, ?)")) {
        ps.setString(1, u);
        ps.setString(2, p);
        ps.executeUpdate();
        return true;
    } catch (Exception e) {
        e.printStackTrace();
    }
    return false;
}
}

*********************CONEXION************************
/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 * Click nbfs://nbhost/SystemFileSystem/Templates/Classes/Class.java to edit this template
 */
package practica;

import java.sql.Connection;
import java.sql.DriverManager;
import javax.swing.JOptionPane;

/**
 *
 * @author Admin
 */
public class Conexion {
   public static Connection conectar(){
        Connection conn = null;
        try {
            Class.forName("com.mysql.jdbc.Driver");
            conn=DriverManager.getConnection("jdbc:mysql://localhost/sistema","root","");
            JOptionPane.showMessageDialog(null, "OK estas conectado");
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(null, ex);
        }
        return conn;
    }
    
}


*********************INIT**********************
public boton() {
        initComponents();
        jTable1.setModel(new UsuarioDAo().obtenerIngresos());
    }
   
    public void ingresar(){
        UsuarioDAo dao = new UsuarioDAo();
    // Valida en la base de datos y registra el ingreso si es correcto
    if (dao.validarYRegistrar(jTextusuario.getText(), jTextclave.getText())) {
        javax.swing.JOptionPane.showMessageDialog(this, "Bienvenido al sistema");
        
        // Actualiza la tabla con los ingresos
        jTable1.setModel(dao.obtenerIngresos());
        
        // Limpia los campos para permitir el ingreso de otro usuario
        jTextusuario.setText(""); 
        jTextclave.setText(""); 
    } else {
        javax.swing.JOptionPane.showMessageDialog(this, "Usuario o clave incorrecta");
    }
}
    public void registrar(){
        UsuarioDAo dao = new UsuarioDAo();

// Registra los datos que el usuario escribió en las cajas de texto
boolean registrado = dao.registrarNuevoUsuario(jTextusuario.getText(), jTextclave.getText());

if (registrado) {
    javax.swing.JOptionPane.showMessageDialog(this, "¡Usuario registrado con éxito en el sistema!");
    jTextusuario.setText("");
    jTextclave.setText("");
} else {
    javax.swing.JOptionPane.showMessageDialog(this, "Error al registrar el usuario");
}
    }
    

***************************CATEGORIA************************************
/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 * Click nbfs://nbhost/SystemFileSystem/Templates/Classes/Class.java to edit this template
 */
package categor;
import java.sql.*;
import javax.swing.table.DefaultTableModel;
import practica.Conexion;
/**
 *
 * @author Admin
 */
public class ProductoDao {

    // Método para cargar las categorías en el JComboBox
    public void cargarCategoriasEnCombo(javax.swing.JComboBox combo) {
        String sql = "SELECT nombre_categoria FROM categoria";
        try (Connection con = conexion.conectar();
             Statement st = con.createStatement();
             ResultSet rs = st.executeQuery(sql)) {
            combo.removeAllItems();
            while (rs.next()) {
                combo.addItem(rs.getString("nombre_categoria"));
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // Método para insertar (genera un ID aleatorio o basado en tiempo para cumplir con char(20))
    public boolean insertar(String nom, double precio, String cat) {
        String idProductoUnico = "PROD-" + System.currentTimeMillis(); // Genera un código único tipo texto
        
        String sql = "INSERT INTO productos (id_producto, nombre_producto, precio, id_categoria) " +
                     "SELECT ?, ?, ?, id_categoria FROM categoria WHERE nombre_categoria = ?";
        
        try (Connection con = conexion.conectar(); PreparedStatement ps = con.prepareStatement(sql)) {
            ps.setString(1, idProductoUnico);
            ps.setString(2, nom);
            ps.setDouble(3, precio);
            ps.setString(4, cat);
            return ps.executeUpdate() > 0;
        } catch (Exception e) { 
            e.printStackTrace();
            return false; 
        }
    }

    // Método para buscar por categoría
    public DefaultTableModel buscar(String cat) {
        DefaultTableModel m = new DefaultTableModel(new String[]{"ID Producto", "Producto", "Precio", "Categoría"}, 0);
        String sql = "SELECT p.id_producto, p.nombre_producto, p.precio, c.nombre_categoria " +
                     "FROM productos p JOIN categoria c ON p.id_categoria = c.id_categoria " +
                     "WHERE c.nombre_categoria = ?";
        
        try (Connection con = conexion.conectar(); PreparedStatement ps = con.prepareStatement(sql)) {
            ps.setString(1, cat);
            ResultSet rs = ps.executeQuery();
            while (rs.next()) {
                m.addRow(new Object[]{rs.getString(1), rs.getString(2), rs.getDouble(3), rs.getString(4)});
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return m;
    }
}

*********************CONEXION***************************
public class conexion {
     public static Connection conectar(){
        Connection conn = null;
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            conn=DriverManager.getConnection("jdbc:mysql://localhost/gestion_productos","root","");
            JOptionPane.showMessageDialog(null, "OK estas conectado");
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(null, ex);
        }
        return conn;
    }
    
}
******************INIT***********************
  public prueba() {
        initComponents();
        new ProductoDao().cargarCategoriasEnCombo(jCombocategoria);
    }
    
    public void guardar(){
        ProductoDao dao = new ProductoDao();
if(dao.insertar(jTextnombre.getText(), Double.parseDouble(jTextprecio.getText()), 
        jCombocategoria.getSelectedItem().toString())) {
    javax.swing.JOptionPane.showMessageDialog(this, "Guardado con éxito");
    jTextnombre.setText(""); jTextprecio.setText("");
} else {
    javax.swing.JOptionPane.showMessageDialog(this, "Error al guardar");
}
    }
    public void buscar(){
        jTablaresultado.setModel(new ProductoDao().
                buscar(jCombocategoria.getSelectedItem().toString()));
    }

    
