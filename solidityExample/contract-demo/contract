pragma solidity >=0.5.11;

//import "github.com/oraclize/ethereum-api/oraclizeAPI.sol";
//import "github.com/Arachnid/solidity-stringutils/strings.sol";
pragma experimental ABIEncoderV2;

contract CatchPet {

    struct Pet {
        string  Type;
        uint    Point;
        uint    Repertory;
    }

    struct PetMsg {
        uint    PetID;
        string  Type;
        string  Name;
        uint    Point;
        bool    IsSaled;
    }

    struct CatchRecord {
        string  Type;
        uint    Height;
        uint    Time;
    }

    struct SaleRecord {
        string  Type;
        uint    Height;
        uint    Point;
        uint    Time;
    }

    mapping(string => Pet) pets;
    mapping(address => PetMsg[]) petMsges;
    mapping(address => uint) pID;
    mapping(address => uint) point;
    mapping(address => CatchRecord[]) catchRecord;
    mapping(address => SaleRecord[]) saleRecord;

    // game 参数
    uint constant SEEDINFO = 0x0000000000002000000000100000000008000000000400000000020000000001;

    constructor () public {
        // pets warehouse init
        pets["dog"].Type = "dog";
        pets["dog"].Point = 40;
        pets["dog"].Repertory = 10;

        pets["cat"].Type = "cat";
        pets["cat"].Point = 20;
        pets["cat"].Repertory = 20;

        pets["monkey"].Type = "monkey";
        pets["monkey"].Point = 100;
        pets["monkey"].Repertory = 4;

        pets["panda"].Type = "panda";
        pets["panda"].Point = 200;
        pets["panda"].Repertory = 2;

    }

    function GetPetRepertory(string memory _petType) public view returns(Pet memory){
        Pet memory pet = pets[_petType];
        return pet;
    }

    function Catch() external payable returns(string memory) {
        require(msg.value >= 50000000000, "transfer value must great than 50000000000");

        uint blockNumber = block.number;
        bytes32 entropy = keccak256(abi.encodePacked(uint(SEEDINFO),blockhash(blockNumber)));

        uint petSite = uint(entropy) % 36;
        string memory petType = getPetType(petSite);

        PetMsg memory _newPet;
        _newPet.Name = "";
        _newPet.Type = petType;
        _newPet.PetID = pID[msg.sender];
        _newPet.Point = pets[petType].Point;
        petMsges[msg.sender].push(_newPet);
        _newPet.IsSaled = false;
        pID[msg.sender] += 1;

        CatchRecord memory _cRecord;
        _cRecord.Type = petType;
        _cRecord.Height = blockNumber;
        _cRecord.Time = now;
        catchRecord[msg.sender].push(_cRecord);

        return petType;
    }

    function GetPlayerPets() public view returns(PetMsg[] memory) {
        return petMsges[msg.sender];
    }

    function GetCatchRecord() public view returns(CatchRecord[] memory) {
        return catchRecord[msg.sender];
    }

    function Sale(uint _petID) public {

        uint index;
        bool ifExit;
        (index, ifExit) = getPetByID(_petID);
        if (ifExit) {
            point[msg.sender] +=petMsges[msg.sender][index].Point;

            SaleRecord memory sr;
            sr.Type = petMsges[msg.sender][index].Type;
            sr.Height = block.number;
            sr.Point = petMsges[msg.sender][index].Point;
            sr.Time = now;

            saleRecord[msg.sender].push(sr);
            petMsges[msg.sender][index].IsSaled = true;
        }
    }

    function GetSaleRecord() public view returns(SaleRecord[] memory) {
        return saleRecord[msg.sender];
    }

    function ChangePetName(string memory _name, uint _id) public {
        uint index;
        bool ifExit;
        (index, ifExit) = getPetByID(_id);
        if (ifExit) {
            petMsges[msg.sender][index].Name = _name;
        }

    }

    function GetPlayerPoint() public view returns(uint) {
        return point[msg.sender];
    }

    function getPetByID(uint _petID) internal returns(uint, bool) {
        PetMsg[] storage petStore = petMsges[msg.sender];
        for (uint i = 0; i < petStore.length; i++) {
            if ((petStore[i].PetID == _petID) && !petStore[i].IsSaled) {
                return (i, true);
            }
        }

        return (0, false);
    }

    function getPetType(uint _peyID) internal returns(string memory) {
        if (_peyID >= 0 && _peyID < 20) {
            return "cat";
        } else if (_peyID >= 20 && _peyID < 30) {
            return "dog";
        } else if (_peyID >= 30 && _peyID < 34) {
            return "monkey";
        } else if (_peyID >= 34 && _peyID < 36) {
            return "panda";
        } else {
            return "";
        }
    }

}