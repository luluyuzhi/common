```c++
union nullptr_r {
	template<class T>
	bool operator==(T* t)
	{
		return reinterpret_cast<void*>(i)== t;
	}

	bool operator==(nullptr_t)
	{
		return reinterpret_cast<void*>(i) == nullptr;
	}
private:
	inline constexpr static int i = 0;
} nullptt;


std::cout << (nullptt == nullptr); //1
```

