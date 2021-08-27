# C++ 函数工具包





```cpp
#include "OneDriveUtil.h"


CJsonUtil::CJsonUtil(const std::string &strContent) : m_strBody(strContent)
{
}

bool CJsonUtil::jsonValid()
{
	if (m_strBody.empty())
	{
		return false;
	}
	m_jsonDoc.Parse(m_strBody.c_str());
	return !m_jsonDoc.HasParseError();
}

std::vector<std::string> CJsonUtil::getAllKeys(const rapidjson::Value &obj)
{
	std::vector<std::string> vecKeys;
	for (auto iter = obj.MemberBegin(); iter != obj.MemberEnd(); iter++)
	{
		vecKeys.emplace_back(iter->name.GetString());
	}
	return vecKeys;
}

std::string CJsonUtil::getStringByKey(const std::string &strKey)
{
	auto iter = m_jsonDoc.FindMember(strKey.c_str());
	if (iter != m_jsonDoc.MemberEnd())
	{
		if (iter->value.IsString())
		{
			return iter->value.GetString();
		}
	}
	return "";
}

std::string CJsonUtil::getStringByKey(const rapidjson::Value &v, const std::string &strKey)
{
	auto iter = v.FindMember(strKey.c_str());
	if (iter != v.MemberEnd())
	{
		if (iter->value.IsString())
		{
			return iter->value.GetString();
		}
	}
	return "";
}

rapidjson::Value CJsonUtil::getArrayByKey(const std::string &strKey)
{
	rapidjson::Value v;
	auto iter = m_jsonDoc.FindMember(strKey.c_str());
	if (iter != m_jsonDoc.MemberEnd())
	{
		if (iter->value.IsArray())
		{
			v = iter->value;
		}
	}
	return v;
}

/*
rapidjson::Value CJsonUtil::getObjectByKey(const std::string &strKey)
{
	rapidjson::Value v;
	auto iter = m_jsonDoc.FindMember(strKey.c_str());
	if (iter != m_jsonDoc.MemberEnd())
	{
		if (iter->value.IsObject())
		{
			v = iter->value.GetObject();
		}
	}
	return v;
}*/

const rapidjson::Value& CJsonUtil::getObjectByKey(const rapidjson::Value &inValue, const std::string &strKey)
{
	const char *pKey = strKey.c_str();
	if (inValue.HasMember(pKey)
		&& inValue[pKey].IsObject()
		&& !inValue[pKey].IsNull())
	{
		const rapidjson::Value &v = inValue[pKey];
		return v;
	}
}

unsigned char CEncoding::ToHex(unsigned char x)
{
    return  x > 9 ? x + 55 : x + 48;
}

unsigned char CEncoding::FromHex(unsigned char x)
{
    unsigned char y;
    if (x >= 'A' && x <= 'Z') y = x - 'A' + 10;
    else if (x >= 'a' && x <= 'z') y = x - 'a' + 10;
    else if (x >= '0' && x <= '9') y = x - '0';
    else assert(0);
    return y;
}

std::string CEncoding::UrlEncode(const std::string& str)
{
    std::string strTemp = "";
    size_t length = str.length();
    for (size_t i = 0; i < length; i++)
    {
        if (isalnum((unsigned char)str[i]) ||
            (str[i] == '-') ||
            (str[i] == '_') ||
            (str[i] == '.') ||
            (str[i] == '~'))
            strTemp += str[i];
        else if (str[i] == ' ')
            strTemp += "+";
        else
        {
            strTemp += '%';
            strTemp += ToHex((unsigned char)str[i] >> 4);
            strTemp += ToHex((unsigned char)str[i] % 16);
        }
    }
    return strTemp;
}

std::string CEncoding::UrlDecode(const std::string& str)
{
    std::string strTemp = "";
    size_t length = str.length();
    for (size_t i = 0; i < length; i++)
    {
        if (str[i] == '+') strTemp += ' ';
        else if (str[i] == '%')
        {
            assert(i + 2 < length);
            unsigned char high = FromHex((unsigned char)str[++i]);
            unsigned char low = FromHex((unsigned char)str[++i]);
            strTemp += high * 16 + low;
        }
        else strTemp += str[i];
    }
    return strTemp;
}



std::string mapToJsonString(const std::map <std::string, std::string> &mapObject)
{
	Document document;
	Document::AllocatorType& allocator = document.GetAllocator();
	Value root(kObjectType);

	Value key(kStringType);
	Value value(kStringType);

	for (map<string, string>::const_iterator it = mapObject.begin(); it != mapObject.end(); ++it)  // 注意这里要用const_iterator
	{
		key.SetString(it->first.c_str(), allocator);
		value.SetString(it->second.c_str(), allocator);
		root.AddMember(key, value, allocator);
	}

	StringBuffer buffer;
	Writer<StringBuffer> writer(buffer);
	root.Accept(writer);
	return buffer.GetString();
}



map<string, string> jsonStringToMap(const string& jsonString)
{
	rapidjson::Document doc;
	doc.SetObject();

	doc.Parse<rapidjson::kParseDefaultFlags>(jsonString.c_str());

	map<string, string> m;

	for (rapidjson::Value::MemberIterator iter = doc.MemberBegin(); iter != doc.MemberEnd(); iter++)
	{
		const char * key = iter->name.GetString();
		const rapidjson::Value& val = iter->value;

		if (val.IsDouble())
			m.insert(make_pair(key, to_string(val.GetDouble())));
		else if (val.IsBool())
			m.insert(make_pair(key, to_string(val.GetBool())));
		else if (val.IsInt())
			m.insert(make_pair(key, to_string(val.GetInt())));
		else if (val.IsInt64())
			m.insert(make_pair(key, to_string(val.GetInt64())));
		else if (val.IsString())
			m.insert(make_pair(key, val.GetString()));
	}

	return m;
}



string readFileIntoString(const string &filename)
{
	ifstream infile;
	string strRet;
	string strTmp;

	infile.open(filename);
	if (!infile.is_open())
		return "";

	while (!infile.eof())
	{
		infile >> strTmp;
		strRet += strTmp;
	}
	infile.close();
	
	return strRet;
}


bool gzipInflate(const std::string& compressedBytes, std::string& uncompressedBytes) {
	if (compressedBytes.size() == 0) {
		uncompressedBytes = compressedBytes;
		return true;
	}

	uncompressedBytes.clear();

	unsigned full_length = compressedBytes.size();
	unsigned half_length = compressedBytes.size() / 2;

	unsigned uncompLength = full_length;
	char* uncomp = (char*)calloc(sizeof(char), uncompLength);

	z_stream strm;
	strm.next_in = (Bytef *)compressedBytes.c_str();
	strm.avail_in = compressedBytes.size();
	strm.total_out = 0;
	strm.zalloc = Z_NULL;
	strm.zfree = Z_NULL;

	bool done = false;

	if (inflateInit2(&strm, (16 + MAX_WBITS)) != Z_OK) {
		free(uncomp);
		return false;
	}

	while (!done) {
		// If our output buffer is too small  
		if (strm.total_out >= uncompLength) {
			// Increase size of output buffer  
			char* uncomp2 = (char*)calloc(sizeof(char), uncompLength + half_length);
			memcpy(uncomp2, uncomp, uncompLength);
			uncompLength += half_length;
			free(uncomp);
			uncomp = uncomp2;
		}

		strm.next_out = (Bytef *)(uncomp + strm.total_out);
		strm.avail_out = uncompLength - strm.total_out;

		// Inflate another chunk.  
		int err = inflate(&strm, Z_SYNC_FLUSH);
		if (err == Z_STREAM_END) done = true;
		else if (err != Z_OK) {
			break;
		}
	}

	if (inflateEnd(&strm) != Z_OK) {
		free(uncomp);
		return false;
	}

	for (size_t i = 0; i < strm.total_out; ++i) {
		uncompressedBytes += uncomp[i];
	}
	free(uncomp);
	return true;
}


std::string getMapValue(std::map <std::string, std::string> &cookieItem, const std::string & strKey)
{
	if (cookieItem.count(strKey) != 0) {
		auto strValue = cookieItem[strKey];
		return strValue;
	}
	return "";
}


long long TimesTamp()
{
	struct timeb t;
	ftime(&t);
	return 1000 * t.time + t.millitm;
}


std::string DateFromTimestamp(int64_t nTimetamp)
{
	//621355968000000000是1970-1-1的Ticks值
	unsigned long long nTempA = nTimetamp - 621355968000000000;
	unsigned long long nTempB = nTempA / 10000;
	return FromSec(nTempB /1000);
}

wstring AsciiToUnicode(const string& str) {
	// 预算-缓冲区中宽字节的长度    
	int unicodeLen = MultiByteToWideChar(CP_ACP, 0, str.c_str(), -1, nullptr, 0);
	// 给指向缓冲区的指针变量分配内存    
	wchar_t *pUnicode = (wchar_t*)malloc(sizeof(wchar_t)*unicodeLen);
	// 开始向缓冲区转换字节    
	MultiByteToWideChar(CP_ACP, 0, str.c_str(), -1, pUnicode, unicodeLen);
	wstring ret_str = pUnicode;
	free(pUnicode);
	return ret_str;
}
string UnicodeToAscii(const wstring& wstr) {
	// 预算-缓冲区中多字节的长度    
	int ansiiLen = WideCharToMultiByte(CP_ACP, 0, wstr.c_str(), -1, nullptr, 0, nullptr, nullptr);
	// 给指向缓冲区的指针变量分配内存    
	char *pAssii = (char*)malloc(sizeof(char)*ansiiLen);
	// 开始向缓冲区转换字节    
	WideCharToMultiByte(CP_ACP, 0, wstr.c_str(), -1, pAssii, ansiiLen, nullptr, nullptr);
	string ret_str = pAssii;
	free(pAssii);
	return ret_str;
}
wstring Utf8ToUnicode(const string& str) {
	// 预算-缓冲区中宽字节的长度    
	int unicodeLen = MultiByteToWideChar(CP_UTF8, 0, str.c_str(), -1, nullptr, 0);
	// 给指向缓冲区的指针变量分配内存    
	wchar_t *pUnicode = (wchar_t*)malloc(sizeof(wchar_t)*unicodeLen);
	// 开始向缓冲区转换字节    
	MultiByteToWideChar(CP_UTF8, 0, str.c_str(), -1, pUnicode, unicodeLen);
	wstring ret_str = pUnicode;
	free(pUnicode);
	return ret_str;
}
string UnicodeToUtf8(const wstring& wstr) {
	// 预算-缓冲区中多字节的长度    
	int ansiiLen = WideCharToMultiByte(CP_UTF8, 0, wstr.c_str(), -1, nullptr, 0, nullptr, nullptr);
	// 给指向缓冲区的指针变量分配内存    
	char *pAssii = (char*)malloc(sizeof(char)*ansiiLen);
	// 开始向缓冲区转换字节    
	WideCharToMultiByte(CP_UTF8, 0, wstr.c_str(), -1, pAssii, ansiiLen, nullptr, nullptr);
	string ret_str = pAssii;
	free(pAssii);
	return ret_str;
}
string AsciiToUtf8(const string& str) {
	return UnicodeToUtf8(AsciiToUnicode(str));
}
string Utf8ToAscii(const string& str) {
	return UnicodeToAscii(Utf8ToUnicode(str));
}

// string 与 Int 互转  
int StringToInt(const string& str) {
	return atoi(str.c_str());
}
string IntToString(int i) {
	char ch[1024];
	memset(ch, 0, 1024);
	sprintf_s(ch, sizeof(ch), "%d", i);
	return ch;
}
string IntToString(char i) {
	char ch[1024];
	memset(ch, 0, 1024);
	sprintf_s(ch, sizeof(ch), "%c", i);
	return ch;
}
string IntToString(double i) {
	char ch[1024];
	memset(ch, 0, 1024);
	sprintf_s(ch, sizeof(ch), "%f", i);
	return ch;
}



int WriteFile(const wstring &strFile, size_t size, const char* buff)
{
	if ((0 == size) || (nullptr == buff))
	{
		return -1;
	}

	ofstream ofs;
	ofs.open(strFile, std::ios::binary | std::ios::out | std::ios::trunc);
	if (ofs.fail())
	{
		ofs.close();
		return -1;
	}

	ofs.write(buff, size);
	ofs.close();

	return 0;
}


string formatFileSize(const string &strSize) 
{
	if (strSize.empty())
	{
		return "";
	}

	float fFileSize = 0.0;
	double dSize = 0.0;
	string strUnit;

	try
	{
		fFileSize = boost::lexical_cast<float>(strSize);
	}
	catch (const std::exception&)
	{
		fFileSize = 0;
	}

	if (1024.0 > fFileSize)
	{
		dSize = fFileSize;
		strUnit = "B";
	}
	else if (1024.0 > (fFileSize / 1024.0))
	{
		dSize = fFileSize;
		strUnit = "KB";
	}
	else if (1024.0 > (fFileSize / (1024.0*1024.0)))
	{
		dSize = fFileSize / (1024.0*1024.0);
		strUnit = "MB";
	}
	else if (1024.0 > (fFileSize / (1024.0 * 1024.0 * 1024.0)))
	{
		dSize = fFileSize / (1024.0 * 1024.0 * 1024.0);
		strUnit = "GB";
	}

	boost::format fmt;
	fmt = boost::format("%.2f%s") % dSize % strUnit;
	return fmt.str();
}


/**
 * 字符串替换函数
 * #function name   : ReplaceStr()
 * #param str       : 操作之前的字符串
 * #param before    : 将要被替换的字符串
 * #param after     : 替换目标字符串
 * #return          : void
 */
void ReplaceStr(std::string& str, const std::string& before, const std::string& after)
{
	for (std::string::size_type pos(0); pos != std::string::npos; pos += after.length())
	{
		pos = str.find(before, pos);
		if (pos != std::string::npos)
			str.replace(pos, before.length(), after);
		else
			break;
	}
}

```

