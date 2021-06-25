```
package kr.co.ntek.android.avl.busan.utils;

import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.ImageView;
import android.widget.TextView;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.recyclerview.widget.RecyclerView;

import org.w3c.dom.Text;

import java.util.ArrayList;

import kr.co.ntek.android.avl.busan.R;
import kr.co.ntek.android.avl.busan.model.FinderItem;

public class FinderAdapter extends RecyclerView.Adapter<FinderAdapter.ViewHolder> {


    private ArrayList<FinderItem> mFinderList;

    @NonNull
    @Override
    public FinderAdapter.ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_finder,parent,false);
        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull FinderAdapter.ViewHolder holder, int position) {
        holder.onBind(mFinderList.get(position));
    }

    public void setFinderList(ArrayList<FinderItem> list){
        Log.d("OnClickResult", "setFinderList 사이즈 : " + list.size());
        this.mFinderList = list;
        Log.d("OnClickResult", "setFinderList");
        notifyDataSetChanged();
    }

    @Override
    public int getItemCount() {
        Log.d("TestFragment","호출값 : " + mFinderList.size());
        return mFinderList.size();
    }


    class ViewHolder extends RecyclerView.ViewHolder{
        ImageView marker;
        TextView address, name;
        Button detail;

        public ViewHolder(@NonNull View itemView) {
            super(itemView);

            marker = (ImageView) itemView.findViewById(R.id.marker);
            name = (TextView) itemView.findViewById(R.id.name);
            address = (TextView) itemView.findViewById(R.id.address);
            detail = (Button) itemView.findViewById(R.id.detail);
        }
        void onBind(FinderItem item){
            switch(item.market_type){
                case "1":
                    marker.setImageResource(R.drawable.map_poi_03);
                    break;
                case "2":
                    marker.setImageResource(R.drawable.map_poi_04);
                    break;
                case "3":
                    marker.setImageResource(R.drawable.map_poi_03);
                    break;
                case "4":
                    marker.setImageResource(R.drawable.map_poi_04);
                    break;
                case "5":
                    marker.setImageResource(R.drawable.map_poi_05);
                    break;
                case "6":
                    marker.setImageResource(R.drawable.map_poi_03);
                    break;
            }
            name.setText(item.getName());
            address.setText(item.getAddress());
            detail.setOnClickListener(new View.OnClickListener(){

                @Override
                public void onClick(View v) {
                    Log.d("ClickEvent", "클릭됨 : " + name.getText().toString() + " " + address.getText().toString());
                }
            });
        }


    }
}
```
package kr.co.ntek.android.avl.busan.model;

public class Common_Model {
    public String id;
    public String market_type;
    public String lat;
    public String lon;
    public String name;
    public String address;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getMarket_type() {
        return market_type;
    }

    public void setMarket_type(String market_type) {
        this.market_type = market_type;
    }

    public String getLat() {
        return lat;
    }

    public void setLat(String lat) {
        this.lat = lat;
    }

    public String getLon() {
        return lon;
    }

    public void setLon(String lon) {
        this.lon = lon;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}

```

```
package kr.co.ntek.android.avl.busan.view;

import android.app.FragmentManager;
import android.os.Bundle;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.CheckBox;
import android.widget.EditText;
import android.widget.Toast;

import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.fragment.app.DialogFragment;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;
import androidx.room.Room;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import kr.co.ntek.android.avl.busan.R;
import kr.co.ntek.android.avl.busan.model.Common_Model;
import kr.co.ntek.android.avl.busan.model.Dgr;
import kr.co.ntek.android.avl.busan.model.FinderItem;
import kr.co.ntek.android.avl.busan.model.Hosp;
import kr.co.ntek.android.avl.busan.model.Seocenter;
import kr.co.ntek.android.avl.busan.model.Youngsu;
import kr.co.ntek.android.avl.busan.utils.AppDatabase;
import kr.co.ntek.android.avl.busan.utils.DataAdapter;
import kr.co.ntek.android.avl.busan.utils.FinderAdapter;

import static kr.mappers.atlansdk.CommonData.getApplicationContext;

public class FinderDialogFragment extends DialogFragment {

    private RecyclerView mRecyclerView;
    private FinderAdapter mRecyclerAdapter;
    private ArrayList<FinderItem> list; // 리사이클러뷰 전용 List

    private List<Dgr> dgr_List;
    private ArrayList<Hosp> hosp_List;
    private ArrayList<Youngsu> youngsu_List;
    private ArrayList<Seocenter> seocenter_List;

    private AppDatabase db; // room
    private Button button_Find;

    public FinderDialogFragment(){}
    public static FinderDialogFragment getInstance() {
        FinderDialogFragment e = new FinderDialogFragment();
        return e;
    }

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View rootView = inflater.inflate(R.layout.finder_fragment, container, false);
        db = Room.databaseBuilder(getApplicationContext(), AppDatabase.class, "POI").build();
        list = new ArrayList<>();

        mRecyclerView = (RecyclerView) rootView.findViewById(R.id.recyclerview);
        mRecyclerAdapter = new FinderAdapter();

        mRecyclerView.setAdapter(mRecyclerAdapter);
        mRecyclerView.setLayoutManager(new LinearLayoutManager(getActivity()));
        mRecyclerAdapter.setFinderList(list);
        return rootView;
    }

    @Override
    public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        button_Find = view.findViewById(R.id.button_Find);
        EditText editText_search = view.findViewById(R.id.editText_search);
        CheckBox checkBox1 = view.findViewById(R.id.checkBox_1);
        CheckBox checkBox2 = view.findViewById(R.id.checkBox_2);
        CheckBox checkBox3 = view.findViewById(R.id.checkBox_3);
        CheckBox checkBox4 = view.findViewById(R.id.checkBox_4);
        CheckBox checkBox5 = view.findViewById(R.id.checkBox_5);
        CheckBox checkBox6 = view.findViewById(R.id.checkBox_6);

        button_Find.setOnClickListener(new View.OnClickListener(){
            @Override
            public void onClick(View v) {
                list.clear();
                List<String> checkList = new ArrayList<>();
                if(checkBox1.isChecked()) checkList.add("1");
                if(checkBox2.isChecked()) checkList.add("2");
                if(checkBox3.isChecked()) checkList.add("3");
                if(checkBox4.isChecked()) checkList.add("4");
                if(checkBox5.isChecked()) checkList.add("5");
                if(checkBox6.isChecked()) checkList.add("6");

                DataAdapter mDbHelper = new DataAdapter(getApplicationContext());
                mDbHelper.createDatabase();
                mDbHelper.open();
                List<Common_Model> data_List = mDbHelper.getData(checkList, editText_search.getText().toString());

                for(int i=0; i<data_List.size(); i++){
                    list.add(new FinderItem(data_List.get(i).getId(),data_List.get(i).getMarket_type(), data_List.get(i).lat, data_List.get(i).getLon(), data_List.get(i).getName(), data_List.get(i).getAddress()));
                }
                mRecyclerAdapter.setFinderList(list);
            }
        });
    }

    public void getDgr(){
        Thread thread = new Thread(new Runnable() {
            @Override
            public void run() {
                db.dgrDao().getAll();
            }
        });
    }
}

```

```

```
