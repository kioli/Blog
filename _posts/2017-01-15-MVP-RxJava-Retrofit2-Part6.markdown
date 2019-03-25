---
title: "MVP with Retrofit2 and RxJava2 - 7"
last_modified_at: 2017-01-15T12:00:00+01:00
header: 
  overlay_image: /assets/images/post/001-mvp/bg-post.jpg
classes: wide
excerpt: "Contract implementation"
categories:
  - Android
tags:
  - MVP
  - Retrofit2
  - Rxjava2
---

Now that we all all the pieces of our big nice jigsaw we can end it with the last tiles, the implementations of the View (**MyView**), Presenter (**MyPresenter**), Model (**MyModel**) and Repository (**MyRepository**)

&nbsp;

# MyView

{% highlight java %}
public class MyView extends InjectingActivity implements MVPMyView {

   @BindView(R.id.loading)
   RelativeLayout spinner;
   @BindView(R.id.recycler_view)
   RecyclerView recyclerView;
   @BindView(R.id.swipe_refresh_layout)
   SwipeRefreshLayout refreshView;

   @State
   ArrayList<MyModel> data;

   private MVPMyPresenter _presenter;

   @Override
   public void onCreate(final Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);

      setupPresenter();

      recyclerView.setHasFixedSize(true);
      recyclerView.setLayoutManager(new LinearLayoutManager(this));
      recyclerView.addItemDecoration(new SimpleDivider(this));

      refreshView.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
         @Override
         public void onRefresh() {
            requestData(true);
            refreshView.setRefreshing(false);
         }
      });
      
      requestData(false);
   }

   private void setupPresenter() {
      _presenter = PresenterHolder.getInstance().getPresenter(ShowPresenter.class);
      if (_presenter == null) {
          _presenter = MyPresenter();
      }
      _presenter.attachView(this);
   }

   @Override
   public void showLoading() {
      spinner.setVisibility(View.VISIBLE);
   }

   @Override
   public void hideLoading() {
      spinner.setVisibility(View.GONE);
   }

   @Override
   public void requestData(final boolean forceRefresh) {
      _presenter.getListPeople(forceRefresh);
   }

   @Override
   public void sendDataToView(List{% raw %}<{% endraw %}MyModel> showList) {
      data = new ArrayList<>(showList);
      setItems();
   }
   
   private void setItems() {
      recyclerView.setAdapter(new MyAdapter(data, this));
   }

   @Override
   protected void onDestroy() {
      super.onDestroy();
      _presenter.detachView();
   }
   
   @Override
   public void onSaveInstanceState(final Bundle outState) {
      super.onSaveInstanceState(outState);
      PresenterHolder.getInstance().putPresenter(MyPresenter.class, _presenter);
   }

   @Override
   protected int getLayoutResId() {
      return R.layout.view_main;
   }
}
{% endhighlight %}

* Uses **Butterknife** (@Bindview) and **IcePick** (@State) as explained before

* Creates the Presenter object and attaches itself to it (so they can communicate)

* Does common initialization for RecyclerView (boring!) and sets a refresh list listener (yawn!)

* Reestablishes the Subscription so when the activity is re-created, if there is any result pending from a previous Single this gets used

* Detaches itself from the presenter on *onDestroy()*

&nbsp;

# MyPresenter

{% highlight java %}
class MyPresenter extends BasePresenter<MVPMyView> implements MVPMyPresenter {

   private MyModel model = new MyModel();
   private ArrayList<Person> people = new ArrayList<>();

   @Override
   public void getListPeople(final boolean forceRefresh) {
      if (!forceRefresh && !people.isEmpty() && getView() != null) {
	     getView().sendDataToView(people);
         return;
      }

      checkViewAttached();
      getView().showLoading();
      model.getPeople(this);
   }

   @Override
   public void callbackForData(@NonNull final List<Person> people) {
	  this.people.clear();
      this.people.addAll(people);
      getView().hideLoading();
      getView().sendDataToView(this.people);
   }
}
{% endhighlight %}

* It returns immediately data if it has them and the call was not forcing a refresh, otherwise it asks the model to provide fresh data

* It implements the callback used by the Model to get the list of people and forward them to the view, removing the loading status

&nbsp;

# MyModel

{% highlight java %}
public class MyModel implements MyContract.ShowModel {
	private MyRepository repository = new MyRepository(App.getInstance().getServiceGenerator());

	@Override
	public void getPeople(final MyPresenter callback) {
		repository.fetchPeople()
				.subscribeOn(DEFAULT_SUBSCRIBE_ON_SCHEDULER)
				.observeOn(DEFAULT_OBSERVE_ON_SCHEDULER)
				.subscribeWith(new DisposableSubscriber<List<Person>>() {
					final ArrayList<Person> result = new ArrayList<>();

					@Override
					public void onNext(final List<Person> o) {
						result.addAll(o);
					}

					@Override
					public void onError(final Throwable t) {
						callback.callbackForData(new ArrayList<Person>());
					}

					@Override
					public void onComplete() {
						callback.callbackForData(result);
					}
				});
	}
}
{% endhighlight %}

* It creates its instance of the repository (it could be made better to have it singleton but 'oh well...' and tells it to fetch the list of people needed by the Presenter, handling the cases for *onCompleted()*, *onError()* and *onNext()* (RxJava specifics)

&nbsp;

# MyRepository

{% highlight java %}
class MyRepository extends BaseRepository {

   private String CACHE_PREFIX_GET_PEOPLE = "people";
   private ServiceGenerator _service;

   MyRepository(@NonNull final ServiceGenerator service) {
      _service = service;
   }

   Flowable fetchPeople() {
      final Single<List<Person>> userNamesSingle = _service.getAPI().getGenderedNames("female");
      final Single<List<Person>> adminNamesSingle = _service.getAPI().getLocalisedNames("Germany");
      return cacheFlowable(CACHE_PREFIX_GET_PEOPLE, adminNamesSingle.mergeWith(userNamesSingle)).cache();
	}
}
{% endhighlight %}

* It takes two Single meant to return a list of people and merges them together to combine their result in one unique stream of data and adds it to the cache present in the **BaseRepository** 

This allows us to combine the two calls we make (yes, yes, they have hardcoded values and this is **bad practice**, but for sake of "shortness" let's assume they are properly done) to receive them as one result, and the caching works for the cases when the user would turn the phone (or change configuration) meanwhile the operation is running, preventing the information from getting lost

&nbsp;

Whoa! Guys we reached the end, I hope you all enjoyed these pages and got out of the quest with some bits and pieces more of knowledge or understanding about these wonderful topics.

Do not hesitate to give me feedback or remarks on the article and the [app][app] and to comment whether you liked this post or not

Till the next time :)

[app]: https://github.com/kioli/MvpRetroRxExample