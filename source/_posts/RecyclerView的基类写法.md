title: RecyclerView的基类写法
date: 2015-11-17 11:01:47
categories:
keywords:
tags: android
---

# RecyclerView的基类写法

### KLBaseRecyclerAdapter\<T\> 

	package com.xxx.recyclerviewdemo.adapter;

	import android.content.Context;
	import android.support.v7.widget.RecyclerView;
	import android.view.LayoutInflater;
	import android.view.View;
	import android.view.ViewGroup;

	import java.util.ArrayList;
	import java.util.List;

	/**
	 * Created by WangQing on 15/11/16.
	 */
	public abstract class KLBaseRecyclerAdapter<T> extends RecyclerView.Adapter {

	    protected Context mContext;
	    protected LayoutInflater mInflater;
	    protected List<T> datas = new ArrayList<T>();

	    private View.OnClickListener onClickListener ;

	    public KLBaseRecyclerAdapter(Context context) {
	        super();
	        this.mContext = context;
	        this.datas = new ArrayList<T>();
	        this.mInflater = (LayoutInflater) mContext.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
	    }

	    public KLBaseRecyclerAdapter(Context context, List<T> datas) {
	        super();
	        this.mContext = context;
	        this.datas.addAll(datas);
	        this.mInflater = (LayoutInflater) mContext.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
	    }

	    public void setOnClickListener(View.OnClickListener onClickListener) {
	        this.onClickListener = onClickListener;
	    }

	    /**
	     * 加载数据
	     *
	     * @param datas
	     */
	    public void setData(List<T> datas) {
	        if (null != datas) {
	//            if (this.datas.size() > 0) {
	//                this.datas.clear();
	//            }
	            this.datas.addAll(datas);
	            notifyDataSetChanged();
	        }
	    }

	    public List<T> getData() {
	        return this.datas;
	    }

	    public T getOneData(int potion) {
	        return datas.get(potion);
	    }

	    /**
	     * 上拉加载数据
	     *
	     * @param datas
	     */
	    public void addData(List<T> datas) {
	        if (null != datas) {
	            this.datas.addAll(datas);
	            notifyDataSetChanged();
	        }
	    }

	    /**
	     * 清除数据源
	     */
	    public void clearData() {
	        if (datas != null) {
	            datas.clear();
	            notifyDataSetChanged();
	        }
	    }

	    @Override
	    public long getItemId(int position) {
	        return position;
	    }

	    @Override
	    public int getItemCount() {
	        return datas != null ? datas.size() : 0;
	    }

	    @Override
	    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
	        View view = mInflater.inflate(setConvertView(), parent, false);
	        return setViewHolder(view);
	    }

	    @Override
	    public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
	        onBindViewHolder((KLViewHolder)holder, position);
	    }

	    public abstract int setConvertView();
	    
	    public abstract RecyclerView.ViewHolder setViewHolder(View view);

	    public abstract void onBindViewHolder(KLViewHolder holder, int position);

	    abstract class KLViewHolder extends RecyclerView.ViewHolder {

	        public KLViewHolder(View convertView) {
	            super(convertView);

	            initView(convertView);

	            if (onClickListener != null)
	                convertView.setOnClickListener(onClickListener);
	        }

	        abstract void initView(View convertView);
	    }
	}


### 实现：
	package com.xxx.recyclerviewdemo.adapter;

	import android.content.Context;
	import android.support.v7.widget.RecyclerView;
	import android.view.View;
	import android.widget.TextView;

	import com.zhuyongit.recyclerviewdemo.R;
	import com.zhuyongit.recyclerviewdemo.bean.NewsBean;

	import java.util.List;

	/**
	 * Created by WangQing on 15/11/17.
	 */
	public class TestAdapter extends KLBaseRecyclerAdapter<NewsBean> {

	    public TestAdapter(Context context) {
	        super(context);
	    }

	    public TestAdapter(Context context, List<NewsBean> datas) {
	        super(context, datas);
	    }

	    @Override
	    public int setConvertView() {
	        return R.layout.grid_recycler_item_layout;
	    }

	    @Override
	    public RecyclerView.ViewHolder setViewHolder(View view) {
	        return new ViewHolder(view);
	    }

	    @Override
	    public void onBindViewHolder(KLViewHolder holder, int position) {
	        ViewHolder _viewHolder = (ViewHolder) holder;
	        NewsBean  _newsBean = getData().get(position);
	        _viewHolder.tvTitle.setText(_newsBean.getTitle());
	        _viewHolder.tvContent.setText(_newsBean.getContent());
	    }

	    class ViewHolder extends KLViewHolder{

	        private TextView tvTitle ;
	        private TextView tvContent ;

	        public ViewHolder(View convertView) {
	            super(convertView);
	        }

	        @Override
	        void initView(View convertView) {
	            tvTitle = (TextView) convertView.findViewById(R.id.tvTitle);
	            tvContent = (TextView) convertView.findViewById(R.id.tvContent);
	        }
	    }
	}

	
