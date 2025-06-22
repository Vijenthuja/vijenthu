// =========================
// MainActivity.java
// =========================

package com.eatxpress;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {
    Button btnLogin, btnRegister;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        btnLogin = findViewById(R.id.btnLogin);
        btnRegister = findViewById(R.id.btnRegister);

        btnLogin.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(MainActivity.this, LoginActivity.class));
            }
        });

        btnRegister.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                startActivity(new Intent(MainActivity.this, RegisterActivity.class));
            }
        });
    }
}

// =========================
// LoginActivity.java
// =========================

package com.eatxpress;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;
import androidx.appcompat.app.AppCompatActivity;

public class LoginActivity extends AppCompatActivity {
    EditText email, password;
    Button loginBtn;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_login);

        email = findViewById(R.id.email);
        password = findViewById(R.id.password);
        loginBtn = findViewById(R.id.loginBtn);

        loginBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                String enteredEmail = email.getText().toString();
                String enteredPassword = password.getText().toString();

                if (enteredEmail.equals("user@test.com") && enteredPassword.equals("123456")) {
                    startActivity(new Intent(LoginActivity.this, HomeActivity.class));
                } else {
                    Toast.makeText(LoginActivity.this, "Invalid credentials", Toast.LENGTH_SHORT).show();
                }
            }
        });
    }
}

// =========================
// RegisterActivity.java
// =========================

package com.eatxpress;

import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;

public class RegisterActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_register);
        // Implement registration logic here
    }
}

// =========================
// HomeActivity.java
// =========================

package com.eatxpress;

import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;
import java.util.ArrayList;
import java.util.List;

public class HomeActivity extends AppCompatActivity {
    RecyclerView recyclerView;
    List<FoodItem> foodList;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_home);

        recyclerView = findViewById(R.id.recyclerView);
        recyclerView.setLayoutManager(new LinearLayoutManager(this));

        foodList = new ArrayList<>();
        foodList.add(new FoodItem("Burger", "https://example.com/burger.jpg", 450));
        foodList.add(new FoodItem("Pizza", "https://example.com/pizza.jpg", 800));

        FoodAdapter adapter = new FoodAdapter(this, foodList);
        recyclerView.setAdapter(adapter);
    }
}

// =========================
// FoodItem.java
// =========================

package com.eatxpress;

public class FoodItem {
    private String name;
    private String imageUrl;
    private double price;

    public FoodItem(String name, String imageUrl, double price) {
        this.name = name;
        this.imageUrl = imageUrl;
        this.price = price;
    }

    public String getName() { return name; }
    public String getImageUrl() { return imageUrl; }
    public double getPrice() { return price; }
}

// =========================
// FoodAdapter.java
// =========================

package com.eatxpress;

import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.TextView;
import androidx.recyclerview.widget.RecyclerView;
import com.bumptech.glide.Glide;
import java.util.List;

public class FoodAdapter extends RecyclerView.Adapter<FoodAdapter.ViewHolder> {
    List<FoodItem> foodList;
    Context context;

    public FoodAdapter(Context context, List<FoodItem> foodList) {
        this.context = context;
        this.foodList = foodList;
    }

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(context).inflate(R.layout.food_item, parent, false);
        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(ViewHolder holder, int position) {
        FoodItem item = foodList.get(position);
        holder.name.setText(item.getName());
        holder.price.setText("Rs. " + item.getPrice());
        Glide.with(context).load(item.getImageUrl()).into(holder.image);
    }

    @Override
    public int getItemCount() {
        return foodList.size();
    }

    public class ViewHolder extends RecyclerView.ViewHolder {
        TextView name, price;
        ImageView image;

        public ViewHolder(View itemView) {
            super(itemView);
            name = itemView.findViewById(R.id.foodName);
            price = itemView.findViewById(R.id.foodPrice);
            image = itemView.findViewById(R.id.foodImage);
        }
    }
}


Intent intent = new Intent(CurrentActivity.this, MapActivity.class);
startActivity(intent);



package com.eatxpress.database;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;

public class DBHelper extends SQLiteOpenHelper {

    public static final String DB_NAME = "EatXpress.db";

    public DBHelper(Context context) {
        super(context, DB_NAME, null, 1);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        // Create Users table
        db.execSQL("CREATE TABLE users (" +
                "id INTEGER PRIMARY KEY AUTOINCREMENT, " +
                "name TEXT, " +
                "email TEXT UNIQUE, " +
                "password TEXT)");

        // Create Cart table
        db.execSQL("CREATE TABLE cart (" +
                "id INTEGER PRIMARY KEY AUTOINCREMENT, " +
                "itemName TEXT, " +
                "quantity INTEGER, " +
                "price REAL)");

        // Optional: Create Orders table
        db.execSQL("CREATE TABLE orders (" +
                "id INTEGER PRIMARY KEY AUTOINCREMENT, " +
                "items TEXT, " +
                "total REAL, " +
                "timestamp DATETIME DEFAULT CURRENT_TIMESTAMP)");
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("DROP TABLE IF EXISTS users");
        db.execSQL("DROP TABLE IF EXISTS cart");
        db.execSQL("DROP TABLE IF EXISTS orders");
        onCreate(db);
    }

    // 🔐 User Registration
    public boolean registerUser(String name, String email, String password) {
        SQLiteDatabase db = this.getWritableDatabase();
        ContentValues values = new ContentValues();
        values.put("name", name);
        values.put("email", email);
        values.put("password", password);

        long result = db.insert("users", null, values);
        return result != -1;
    }

    // ✅ User Login
    public boolean loginUser(String email, String password) {
        SQLiteDatabase db = this.getReadableDatabase();
        Cursor cursor = db.rawQuery("SELECT * FROM users WHERE email = ? AND password = ?", new String[]{email, password});
        boolean success = cursor.getCount() > 0;
        cursor.close();
        return success;
    }

    // 🛒 Add to Cart
    public boolean addToCart(String itemName, int quantity, double price) {
        SQLiteDatabase db = this.getWritableDatabase();
        ContentValues values = new ContentValues();
        values.put("itemName", itemName);
        values.put("quantity", quantity);
        values.put("price", price);

        long result = db.insert("cart", null, values);
        return result != -1;
    }

    // 📦 Get All Cart Items
    public Cursor getCartItems() {
        SQLiteDatabase db = this.getReadableDatabase();
        return db.rawQuery("SELECT * FROM cart", null);
    }

    // 🗑️ Clear Cart
    public void clearCart() {
        SQLiteDatabase db = this.getWritableDatabase();
        db.execSQL("DELETE FROM cart");
    }

    // 💸 Place Order (save cart summary)









    XML codings

    Activity_main.xml
    <?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="24dp"
    android:background="@android:color/white">

    <TextView
        android:id="@+id/appTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="EatXpress"
        android:textSize="32sp"
        android:textStyle="bold"
        android:textColor="#FF5722"
        android:layout_marginBottom="32dp"/>

    <Button
        android:id="@+id/btnLogin"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Login"
        android:backgroundTint="#FF5722"
        android:textColor="@android:color/white"
        android:layout_marginBottom="16dp"/>

    <Button
        android:id="@+id/btnRegister"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Register"
        android:backgroundTint="#4CAF50"
        android:textColor="@android:color/white"/>

</LinearLayout>


Home.XML

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:padding="16dp"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/white">

    <TextView
        android:id="@+id/homeTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Menu"
        android:textSize="24sp"
        android:textStyle="bold"
        android:textColor="#000"
        android:layout_marginBottom="8dp" />

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</LinearLayout>

    public boolean placeOrder(String itemsSummary, double total) {
        SQLiteDatabase db = this.getWritableDatabase();
        ContentValues values = new ContentValues();
        values.put("items", itemsSummary);
        values.put("total", total);
        long result = db.insert("orders", null, values);
        return result != -1;
    }

    // 📜 View Order History
    public Cursor getOrders() {
        SQLiteDatabase db = this.getReadableDatabase();
        return db.rawQuery("SELECT * FROM orders ORDER BY timestamp DESC", null);
    }
}

package com.eatxpress;

import android.os.Bundle;
import androidx.annotation.NonNull;
import androidx.fragment.app.FragmentActivity;
import com.google.android.gms.maps.*;
        import com.google.android.gms.maps.model.*;

public class MapActivity extends FragmentActivity implements OnMapReadyCallback {

    private GoogleMap mMap;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_map);

        SupportMapFragment mapFragment = (SupportMapFragment)
                getSupportFragmentManager().findFragmentById(R.id.map);

        if (mapFragment != null) {
            mapFragment.getMapAsync(this);
        }
    }

    @Override
    public void onMapReady(@NonNull GoogleMap googleMap) {
        mMap = googleMap;

        // Example: Set location to Colombo, Sri Lanka
        LatLng deliveryLocation = new LatLng(6.9271, 79.8612);
        mMap.addMarker(new MarkerOptions().position(deliveryLocation).title("Your Order is Here!"));
        mMap.moveCamera(CameraUpdateFactory.newLatLngZoom(deliveryLocation, 15));
    }
}


Login.XML
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="24dp"
    android:background="@android:color/white">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:gravity="center_horizontal">

        <TextView
            android:id="@+id/loginTitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="EatXpress Login"
            android:textSize="28sp"
            android:textStyle="bold"
            android:textColor="#FF5722"
            android:layout_marginBottom="32dp" />

        <EditText
            android:id="@+id/email"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Email Address"
            android:inputType="textEmailAddress"
            android:drawableStart="@android:drawable/ic_dialog_email"
            android:drawablePadding="10dp"
            android:padding="12dp"
            android:background="@android:drawable/edit_text"
            android:layout_marginBottom="16dp" />

        <EditText
            android:id="@+id/password"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Password"
            android:inputType="textPassword"
            android:drawableStart="@android:drawable/ic_lock_idle_lock"
            android:drawablePadding="10dp"
            android:padding="12dp"
            android:background="@android:drawable/edit_text"
            android:layout_marginBottom="24dp" />

        <Button
            android:id="@+id/loginBtn"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Login"
            android:backgroundTint="#FF5722"
            android:textColor="@android:color/white"
            android:padding="12dp" />

    </LinearLayout>
</ScrollView>


Register.xml
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="24dp"
    android:background="@android:color/white">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:gravity="center_horizontal">

        <TextView
            android:id="@+id/registerTitle"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Register"
            android:textSize="28sp"
            android:textStyle="bold"
            android:textColor="#4CAF50"
            android:layout_marginBottom="32dp"/>

        <EditText
            android:id="@+id/fullName"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Full Name"
            android:inputType="textPersonName"
            android:drawableStart="@android:drawable/ic_menu_myplaces"
            android:drawablePadding="10dp"
            android:padding="12dp"
            android:background="@android:drawable/edit_text"
            android:layout_marginBottom="16dp"/>

        <EditText
            android:id="@+id/email"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Email"
            android:inputType="textEmailAddress"
            android:drawableStart="@android:drawable/ic_dialog_email"
            android:drawablePadding="10dp"
            android:padding="12dp"
            android:background="@android:drawable/edit_text"
            android:layout_marginBottom="16dp"/>

        <EditText
            android:id="@+id/password"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Password"
            android:inputType="textPassword"
            android:drawableStart="@android:drawable/ic_lock_idle_lock"
            android:drawablePadding="10dp"
            android:padding="12dp"
            android:background="@android:drawable/edit_text"
            android:layout_marginBottom="16dp"/>

        <EditText
            android:id="@+id/confirmPassword"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="Confirm Password"
            android:inputType="textPassword"
            android:drawableStart="@android:drawable/ic_lock_idle_lock"
            android:drawablePadding="10dp"
            android:padding="12dp"
            android:background="@android:drawable/edit_text"
            android:layout_marginBottom="24dp"/>

        <Button
            android:id="@+id/registerBtn"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Register"
            android:backgroundTint="#4CAF50"
            android:textColor="@android:color/white"
            android:padding="12dp"/>

    </LinearLayout>
</ScrollView>


food.xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:card_view="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    card_view:cardElevation="4dp"
    card_view:cardCornerRadius="8dp"
    android:layout_marginBottom="12dp">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="12dp">

        <ImageView
            android:id="@+id/foodImage"
            android:layout_width="80dp"
            android:layout_height="80dp"
            android:scaleType="centerCrop"
            android:src="@drawable/ic_launcher_background" />

        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:orientation="vertical"
            android:layout_weight="1"
            android:paddingStart="12dp">

            <TextView
                android:id="@+id/foodName"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Food Name"
                android:textSize="18sp"
                android:textStyle="bold" />

            <TextView
                android:id="@+id/foodPrice"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="Rs. 500"
                android:textColor="#888" />
        </LinearLayout>
    </LinearLayout>
</androidx.cardview.widget.CardView>


AndroidManifest.xml
<manifest ... >
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

<application ... >
<meta-data
android:name="com.google.android.geo.API_KEY"
android:value="YOUR_API_KEY_HERE" />

    <!-- MapActivity registration -->
<activity android:name=".MapActivity" />
    </application>
    </manifest>


