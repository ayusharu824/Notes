
Nuget Required: AutoMapper.Extensions.Microsoft.DependencyInjection


define in program.cs
builder.Services.AddAutoMapper(AppDomain.CurrentDomain.GetAssemblies);



first class
public class EmployeeModel 
{
	public int Id{get;set;}
	public string FirstName{get;set;} = "";
	public string LastName{get;set;} = "";
	public string Address{get;set;} = "";
	public DateTime DOB{get;set;}
	public DateTime DOJ{get;set;}
}

DTOs
public class EmployeeDTO
{
	public int Id {get;set;}
	public string FirstName{get;set;}
	public string LastName{get;set;}
	public string Address{get;set;}
	public DateTime DOB{get;set;}
	public DateTime DOJ{get;set;}
}

Profiles
CustomProfiles
public class CustomProfile : Profile  [this Profile is under AutomMapper class]
{
	public CustomProfile(){
		CreateMap<EmployeeModel, EmployeeDto>.ReverseMap();
			// this line means it will map src to dest 
			// the first param in createmap is src and second param is dest
			// and ReverseMap map it in reverse order
	}
}


Now in controller

public EmployeeController(IEmployeeRespository, IMapper mapper)
{
	_employeeRepository = employeeRepository;
		__mapper = mapper;
}

//IMapper is in automapper namespace

[HttpGet]
public IEnumerable<EmployeeDTO> Get(){
	var result = _employeeRepository.GetEmployees();
	// _employeeRepository.GetEmployees(); is returning EmployeeModel
	//to convert it to Employee DTO we will use automapper
	return _mapper.Map<List<EmployeeDTO>>(result);
	//employeeDto is dest and result is src
}



Now let's do some changes in Employee Dto

New Dto

public class EmployeeDTO
{
	public int Id {get;set;}
	//public string FirstName{get;set;}
	public string FullName{get;set;}
	//public string LastName{get;set;}
	public string Address{get;set;}
	//public DateTime DOB{get;set;}
	public DateTime DateOfBirth{get;set;}
	public DateTime DOJ{get;set;}
}

now property names are different for EmployeeDTO and EmployeeModel
so we have to do some changes in profiles

public CustomProfile(){
CreateMap<Models.Employee, Dtos.Employee>()
.ForMember(dest=>dest.FullName, opt=>opt.MapFrom(src=> src.FirstName+ " " + src.LastName))
.ForMember(dest=>dest.DateOfBirth, opt=>opt.MapFrom(src=> src.DOB))
.ReverseMap();
}






public class Invoice{
public int Id{get;set;}
public string Description{get;set;}
public decimal Amount{get;set;}
}