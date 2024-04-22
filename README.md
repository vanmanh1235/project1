#include <iostream>
#include <vector>
#include <string>
#include <iomanip> 

using namespace std;

class SanPham {
protected:
    string tenSP;
    string moTa;
    int maSP;
    int soLuong;
    double giaNhap;
    double giaBan;

public:
    SanPham() : maSP(0), soLuong(0), giaNhap(0.0), giaBan(0.0) {}

    virtual void nhapThongTin() {
        cout << "Nhap ma san pham: ";
        cin >> maSP;
        cout << "Nhap ten san pham: ";
        cin.ignore();
        getline(cin, tenSP);
        cout << "Nhap so luong: ";
        cin >> soLuong;
        cout << "Nhap mo ta: ";
        cin.ignore();
        getline(cin, moTa);
        cout << "Nhap gia nhap: ";
        cin >> giaNhap;
        cout << "Nhap gia ban: ";
        cin >> giaBan;
    }

    virtual double tinhTongTien() const = 0;

    virtual void hienThi() const {
        cout << "San Pham: " << tenSP << ", Mo ta: " << moTa << ", Ma: " << maSP
            << ", So luong: " << soLuong << ", Gia nhap: " << giaNhap << ", Gia ban: " << giaBan << endl;
    }

    int getMaSP() const {
        return maSP;
    }

    double getGiaBan() const {
        return giaBan;
    }

    int getSoLuong() const {
        return soLuong;
    }
    string getTen() {
        return tenSP;
    }
    void setSoLuong(int newQuantity) {
        soLuong = newQuantity;
    }

    bool kiemSoLuong(int soLuongNhap) {
        if (soLuong >= soLuongNhap) {
            soLuong -= soLuongNhap;
            return true;
        }
        return false;
    }
};

class HangCLC : public SanPham {
public:
    double tinhTongTien() const override {
        return getGiaBan() * getSoLuong() * 2;
    }
};

class HangThuong : public SanPham {
public:
    double tinhTongTien() const override {
        return getGiaBan() * getSoLuong() - 100000;
    }
};

class KhachHang {
protected:
    string ten;
    int tuoi;
    int soCMND;

public:
    vector<SanPham*> sanPhams;
    KhachHang(string ten = "", int tuoi = 0, int soCMND = 0) : ten(ten), tuoi(tuoi), soCMND(soCMND) {}

    virtual void nhapThongTin() {
        cout << "Nhap Ten Khach Hang: ";
        cin.ignore();
        getline(cin, ten);
        cout << "Nhap Tuoi: ";
        cin >> tuoi;
        cout << "Nhap CMND: ";
        cin >> soCMND;
    }

    virtual void hienThi() const {
        cout << "KhachHang: " << ten << ", Tuoi: " << tuoi << ", SoCMND: " << soCMND << endl;
    }

    int getSoCMND() const {
        return soCMND;
    }
    virtual int discount(const vector<pair<SanPham*, int>>& products) = 0;

};

class KhachThanhVien : public KhachHang {
    
public:
    int discount(const vector<pair<SanPham*, int>>& products) override {
        int totalDiscount = 0;
        for (const auto& item : products) {
            totalDiscount += 10000 * item.second;
        }
        return totalDiscount;
    }

};
class KhachThuong : public KhachHang {
public:
    int discount(const vector<pair<SanPham*, int>>& products) override {
        return 0;  // Không có giảm giá cho khách hàng thường
    }
};

class HoaDon {
private:
    vector<pair<SanPham*, int>> sanPhams;
    KhachHang* khachHang;
    double tongTien = 0;

public:
    HoaDon(KhachHang* kh) : khachHang(kh), tongTien(0.0) {}

    void themSanPham(SanPham* sp, int soLuongMua) {
        if (sp->kiemSoLuong(soLuongMua)) {
            sanPhams.push_back(make_pair(sp, soLuongMua));
            tongTien += sp->getGiaBan() * soLuongMua;
            cout << soLuongMua << " x " << sp->getTen() << " da duoc them vao hoa don.\n";
        }
        else {
            cout << "So luong san pham khong du.\n";
        }
    }

    void hienThiHoaDon() const {
        cout << "\n----------------------------------------\n";
        cout << "             HOA DON BAN HANG            \n";
        cout << "----------------------------------------\n";
        cout << "So CMND Khach Hang: " << khachHang->getSoCMND() << "\n";
        khachHang->hienThi();
        cout << "----------------------------------------\n";
        cout << "San Pham Da Mua:\n";
        cout << "----------------------------------------\n";
        cout << left << setw(20) << "Ten San Pham"
            << setw(10) << "Ma SP"
            << setw(12) << "So Luong"
            << setw(12) << "Gia Ban"
            << setw(15) << "Thanh Tien\n";
        cout << "----------------------------------------\n";

        double tongTienBanDau = 0;
        for (const auto& ite : sanPhams) {
            SanPham* sp = ite.first;
            int soLuongMua = ite.second;
            soLuongMua = sp->getSoLuong();
           int thanhTien= sp->tinhTongTien();
            cout << left << setw(20) << sp->getTen()
                << setw(10) << sp->getMaSP()
                << setw(12) << soLuongMua
                << setw(12) << fixed << setprecision(2) << sp->getGiaBan()
                << setw(15) << fixed << setprecision(2) << thanhTien << "\n";
            tongTienBanDau += thanhTien;
        }
        cout << "----------------------------------------\n";

        int giamGia = khachHang->discount(sanPhams);

             cout << "Tong Tien ban dau: " << tongTienBanDau << " VND\n";
        cout << "Giam gia: " << giamGia << " VND\n";
        cout << "Tong Tien Hoa Don (Da Giam): " << tongTienBanDau - giamGia << " VND\n";
        cout << "----------------------------------------\n";
    }
};

class QuanLySieuThi {
private:
    vector<SanPham*> sanPhams;
    vector<KhachHang*> khachHangs;
    vector<HoaDon*> hoaDons;

public:
    ~QuanLySieuThi() {
        for (auto sp : sanPhams) delete sp;
        for (auto kh : khachHangs) delete kh;
        for (auto hd : hoaDons) delete hd;
    }

    void themSanPham() {
        int n;
        cout << "So luong san pham them vao: ";
        cin >> n;
        sanPhams.reserve(n);
        for (int i = 0; i < n; i++) {
            int luaChon;
            cout << "Chon Loai San Pham (1 - Hang CLC, 2 - Hang Thuong): ";
            cin >> luaChon;
            SanPham* sp = nullptr;

            if (luaChon == 1) {
                sp = new HangCLC();
            }
            else if (luaChon == 2) {
                sp = new HangThuong();
            }
            sp->nhapThongTin();
            sanPhams.push_back(sp);
        }
    }

    void themKhachHang() {
        int m;
        cout << "Nhap so luong khach hang them vao: ";
        cin >> m;
        khachHangs.reserve(m);
        for (int i = 0; i < m; i++) {
            int luaChon;
            cout << "Chon loai khach hang (1 - Thanh Vien, 2 - Thuong): ";
            cin >> luaChon;
            KhachHang* kh = nullptr;
            if (luaChon == 1) {
                kh = new KhachThanhVien();
            }
            else if (luaChon == 2) {
                kh = new KhachThuong();
            }
            kh->nhapThongTin();
            khachHangs.push_back(kh);
        }
    }

    void taoHoaDon() {
        int n;
        cout << "Nhap so luong hoa don tao: ";
        cin >> n;
        hoaDons.reserve(n);
        for (int i = 0; i < n; i++) {
            cout << "Nhap so CMND: ";
            int soCMND;
            cin >> soCMND;
            KhachHang* kh = nullptr;

            for (auto& khach : khachHangs) {
                if (khach->getSoCMND() == soCMND) {
                    kh = khach;
                    break;
                }
            }
            if (!kh) {
                cout << "Khach hang khong ton tai.\n";
                continue;
            }

            HoaDon* hd = new HoaDon(kh);

            cout << "Bat dau nhap ma san pham (nhap '0' de ket thuc):\n";
            while (true) {
                int maSP;
                int soLuongMua;
                cout << "Nhap ma san pham: ";
                cin >> maSP;
                if (maSP == 0) break;

                cout << "Nhap so luong mua: ";
                cin >> soLuongMua;

                SanPham* sp = nullptr;
                bool timThay = false;
                for (auto& sanPham : sanPhams) {
                    if (sanPham->getMaSP() == maSP) {
                        sp = sanPham;
                        timThay = true;
                        break;
                    }
                }

                if (timThay) {
                    hd->themSanPham(sp, soLuongMua);
                  
                }
                else {
                    cout << "San pham voi ma " << maSP << " khong ton tai.\n";
                }
            }
            hoaDons.push_back(hd);
                      cout << "Hoa don da tao thanh cong.\n";
        }
    }

    void hienThiSanPhams() const {
        for (int i = 0; i < sanPhams.size(); i++) {
            sanPhams[i]->hienThi();
        }
    }

    void hienThiKhachHangs() const {
        for (int i = 0; i < khachHangs.size(); i++) {
            khachHangs[i]->hienThi();
        }
    }

    void hienThiHoaDons() const {
        for (auto& hd : hoaDons) {
            hd->hienThiHoaDon();
        }
    }

    void menu() {
        int luaChon;
        do {
            cout << "\n### Menu ###\n";
            cout << "1. Them san pham\n";
            cout << "2. Them khach hang\n";
            cout << "3. Hien thi san pham\n";
            cout << "4. Hien thi khach hang\n";
            cout << "5. Tao hoa don\n";
            cout << "6. Hien thi hoa don\n";
            cout << "7. Thoat\n";
            cout << "Nhap lua chon: ";
            cin >> luaChon;
            switch (luaChon) {
            case 1:
                themSanPham();
                break;
            case 2:
                themKhachHang();
                break;
            case 3:
                hienThiSanPhams();
                break;
            case 4:
                hienThiKhachHangs();
                break;
            case 5:
                taoHoaDon();
                break;
            case 6:
                hienThiHoaDons();
                break;
            case 7:
                cout << "Thoat...\n";
                return;
            default:
                cout << "Lua chon khong hop le, vui long thu lai.\n";
            }
        } while (true);
    }
};

int main() {
    QuanLySieuThi quanLy;
    quanLy.menu();
  
    return 0;
}
